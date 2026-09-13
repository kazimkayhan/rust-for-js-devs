# Std misc

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 21.1 Std misc

**What it is**

Many other types are provided by the std library to support
things such as:
* Threads
* Channels
* File I/O
These expand beyond what the [primitives] provide. See also:
[primitives] and [the std library][std]
[primitives]: primitives.md
[std]: https://doc.rust-lang.org/std/.

**JS mental model**

Misc standard library: threads, channels, files, processes, args, FFI — Node fs/child_process/worker-ish territory.

**Rust example**

```rust
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        println!("hello from a thread");
    });
    thread::sleep(Duration::from_millis(50));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- APIs return Result for I/O.
- Prefer higher-level crates for production CLI/async sometimes.

_RBE source: `std_misc.md`_

---

### 21.1.1 Threads

**What it is**

Rust threads are OS threads. spawn runs a closure concurrently. Ownership rules plus Send/Sync traits prevent many data races at compile time.

**JS mental model**

std::thread::spawn is like worker threads but shares memory via Send/Sync types — not a free-for-all like mutating shared JS objects.

**Rust example**

```rust
use std::thread;

const NTHREADS: u32 = 10;

// This is the `main` thread
fn main() {
    // Make a vector to hold the children which are spawned.
    let mut children = vec![];

    for i in 0..NTHREADS {
        // Spin up another thread
        children.push(thread::spawn(move || {
            println!("this is thread number {}", i);
        }));
    }

    for child in children {
        // Wait for the thread to finish. Returns a result.
        let _ = child.join();
    }
}
```

**JS equivalent**

```javascript
// Node worker_threads — different model
// Shared memory needs Atomics; no borrow checker
```

**Watch out**

- Join handles wait for finish.
- Panics in threads do not crash main unless joined and handled.
- Data moved into spawn often needs move closures.

_RBE source: `std_misc/threads.md`_

---

#### 21.1.1.1 Testcase: map-reduce

**What it is**

Map-reduce here means: split input into chunks (map phase in parallel threads), then combine results (reduce) on the main thread. It shows threads + ownership of partial results.

**JS mental model**

Split work across threads then join results — classic map-reduce / parallel map pattern.

**Rust example**

```rust
use std::thread;

// This is the `main` thread
fn main() {

    // This is our data to process.
    // We will calculate the sum of all digits via a threaded map-reduce algorithm.
    // Each whitespace separated chunk will be handled in a different thread.
    //
    // TODO: see what happens to the output if you insert spaces!
    let data = "86967897737416471853297327050364959
11861322575564723963297542624962850
70856234701860851907960690014725639
38397966707106094172783238747669219
52380795257888236525459303330302837
58495327135744041048897885734297812
69920216438980873548808413720956532
16278424637452589860345374828574668";

    // Make a vector to hold the child-threads which we will spawn.
    let mut children = vec![];

    /*************************************************************************
     * "Map" phase
     *
     * Divide our data into segments, and apply initial processing
     ************************************************************************/

    // split our data into segments for individual calculation
    // each chunk will be a reference (&str) into the actual data
    let chunked_data = data.split_whitespace();

    // Iterate over the data segments.
    // .enumerate() adds the current loop index to whatever is iterated
    // the resulting tuple "(index, element)" is then immediately
    // "destructured" into two variables, "i" and "data_segment" with a
    // "destructuring assignment"
    for (i, data_segment) in chunked_data.enumerate() {
        println!("data segment {} is \"{}\"", i, data_segment);

        // Process each data segment in a separate thread
        //
        // spawn() returns a handle to the new thread,
        // which we MUST keep to access the returned value
        //
        // 'move || -> u32' is syntax for a closure that:
        // * takes no arguments ('||')
        // * takes ownership of its captured variables ('move') and
        // * returns an unsigned 32-bit integer ('-> u32')
        //
        // Rust is smart enough to infer the '-> u32' from
        // the closure itself so we could have left that out.
        //
        // TODO: try removing the 'move' and see what happens
        children.push(thread::spawn(move || -> u32 {
            // Calculate the intermediate sum of this segment:
            let result = data_segment
                        // iterate over the characters of our segment..
                        .chars()
                        // .. convert text-characters to their number value..
                        .map(|c| c.to_digit(10).expect("should be a digit"))
                        // .. and sum the resulting iterator of numbers
                        .sum();

            // println! locks stdout, so no text-interleaving occurs
            println!("processed segment {}, result={}", i, result);

            // "return" not needed, because Rust is an "expression language", the
            // last evaluated expression in each block is automatically its value.
            result

        }));
    }


    /*************************************************************************
     * "Reduce" phase
     *
     * Collect our intermediate results, and combine them into a final result
     ************************************************************************/

    // combine each thread's intermediate results into a single final sum.
    //
    // we use the "turbofish" ::<> to provide sum() with a type hint.
    //
    // TODO: try without the turbofish, by instead explicitly
    // specifying the type of final_result
    let final_result = children.into_iter().map(|c| c.join().unwrap()).sum::<u32>();

    println!("Final sum result: {}", final_result);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Chunk data, spawn, collect partials, merge.
- For CPU work consider rayon crate later.

_RBE source: `std_misc/threads/testcase_mapreduce.md`_

---

### 21.1.2 Channels

**What it is**

A channel has Sender and Receiver. send moves a value to another thread. This is safer mental model than sharing mutable memory — similar to posting messages between workers.

**JS mental model**

mpsc channels are like message passing / message ports between threads — send values instead of sharing memory.

**Rust example**

```rust
use std::sync::mpsc::{Sender, Receiver};
use std::sync::mpsc;
use std::thread;

static NTHREADS: i32 = 3;

fn main() {
    // Channels have two endpoints: the `Sender<T>` and the `Receiver<T>`,
    // where `T` is the type of the message to be transferred
    // (type annotation is superfluous)
    let (tx, rx): (Sender<i32>, Receiver<i32>) = mpsc::channel();
    let mut children = Vec::new();

    for id in 0..NTHREADS {
        // The sender endpoint can be copied
        let thread_tx = tx.clone();

        // Each thread will send its id via the channel
        let child = thread::spawn(move || {
            // The thread takes ownership over `thread_tx`
            // Each thread queues a message in the channel
            thread_tx.send(id).unwrap();

            // Sending is a non-blocking operation, the thread will continue
            // immediately after sending its message
            println!("thread {} finished", id);
        });

        children.push(child);
    }

    // Here, all the messages are collected
    let mut ids = Vec::with_capacity(NTHREADS as usize);
    for _ in 0..NTHREADS {
        // The `recv` method picks a message from the channel
        // `recv` will block the current thread if there are no messages available
        ids.push(rx.recv());
    }

    // Wait for the threads to complete any remaining work
    for child in children {
        child.join().expect("oops! the child thread panicked");
    }

    // Show the order in which the messages were sent
    println!("{:?}", ids);
}
```

**JS equivalent**

```javascript
// MessageChannel / worker parentPort.postMessage
```

**Watch out**

- tx.clone() for multiple producers.
- rx iterates until all tx dropped.
- Prefer channels to fight shared mutable state.

_RBE source: `std_misc/channels.md`_

---

### 21.1.3 Path

**What it is**

Path is to PathBuf as str is to String. Use Path for viewing path segments and PathBuf when you need to build/modify paths.

**JS mental model**

Path / PathBuf are OS paths — like path module in Node (path.join), distinguishing borrowed vs owned.

**Rust example**

```rust
use std::path::Path;

fn main() {
    // Create a `Path` from an `&'static str`
    let path = Path::new(".");

    // The `display` method returns a `Display`able structure
    let _display = path.display();

    // `join` merges a path with a byte container using the OS specific
    // separator, and returns a `PathBuf`
    let mut new_path = path.join("a").join("b");

    // `push` extends the `PathBuf` with a `&Path`
    new_path.push("c");
    new_path.push("myfile.tar.gz");

    // `set_file_name` updates the file name of the `PathBuf`
    new_path.set_file_name("package.tgz");

    // Convert the `PathBuf` into a string slice
    match new_path.to_str() {
        None => panic!("new path is not a valid UTF-8 sequence"),
        Some(s) => println!("new path is {}", s),
    }
}
```

**JS equivalent**

```javascript
const path = require('path');
path.join('a', 'b');
```

**Watch out**

- PathBuf owns; Path is a slice.
- Do not assume UTF-8 on all platforms — OsString exists.

_RBE source: `std_misc/path.md`_

---

### 21.1.4 File I/O

**What it is**

The `File` struct represents a file that has been opened (it wraps a file
descriptor), and gives read and/or write access to the underlying file. Since many things can go wrong when doing file I/O, all the `File` methods
return the `io::Result<T>` type, which is an alias for `Result<T, io::Error>`. This makes the failure of all I/O operations *explicit*. Thanks to this, the
programmer can see all the failure paths, and is encouraged to handle them in
a proactive manner.

**JS mental model**

std::fs::File is like fs.open Sync APIs — explicit Result errors.

**Rust example**

```rust
use std::fs::File;
use std::io::prelude::*;

fn main() -> std::io::Result<()> {
    let mut f = File::create("/tmp/rbe_demo.txt")?;
    f.write_all(b"hi")?;
    Ok(())
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use BufReader/BufWriter for efficiency.
- Close happens on Drop (RAII).

_RBE source: `std_misc/file.md`_

---

#### 21.1.4.1 open

**What it is**

File::open(path) opens an existing file for reading. It returns Result<File>. On success you get a handle; on failure you get an error (not found, permissions, ...).

**JS mental model**

File::open reads existing files — like fs.readFile but with a handle.

**Rust example**

```rust
use std::fs::File;
use std::io::prelude::*;
use std::path::Path;

fn main() {
    // Create a path to the desired file
    let path = Path::new("hello.txt");
    let display = path.display();

    // Open the path in read-only mode, returns `io::Result<File>`
    let mut file = match File::open(&path) {
        Err(why) => panic!("couldn't open {}: {}", display, why),
        Ok(file) => file,
    };

    // Read the file contents into a string, returns `io::Result<usize>`
    let mut s = String::new();
    match file.read_to_string(&mut s) {
        Err(why) => panic!("couldn't read {}: {}", display, why),
        Ok(_) => print!("{} contains:\n{}", display, s),
    }

    // `file` goes out of scope, and the "hello.txt" file gets closed
}
```

**JS equivalent**

```javascript
const fs = require('fs');
fs.readFileSync('file.txt', 'utf8');
```

**Watch out**

- Returns Err if missing.
- Combine with Read trait methods.

_RBE source: `std_misc/file/open.md`_

---

#### 21.1.4.2 create

**What it is**

File::create(path) creates or truncates a file for writing. Be careful: existing content is wiped. For more control use OpenOptions.

**JS mental model**

File::create truncates/creates for writing — like opening with write flag.

**Rust example**

```rust
static LOREM_IPSUM: &str =
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non
proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
";

use std::fs::File;
use std::io::prelude::*;
use std::path::Path;

fn main() {
    let path = Path::new("lorem_ipsum.txt");
    let display = path.display();

    // Open a file in write-only mode, returns `io::Result<File>`
    let mut file = match File::create(&path) {
        Err(why) => panic!("couldn't create {}: {}", display, why),
        Ok(file) => file,
    };

    // Write the `LOREM_IPSUM` string to `file`, returns `io::Result<()>`
    match file.write_all(LOREM_IPSUM.as_bytes()) {
        Err(why) => panic!("couldn't write to {}: {}", display, why),
        Ok(_) => println!("successfully wrote to {}", display),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Overwrites existing content.
- Use OpenOptions for append/create_new.

_RBE source: `std_misc/file/create.md`_

---

#### 21.1.4.3 read_lines

**What it is**

To read lines efficiently, wrap a File in BufReader and call lines(). You get an iterator of Result<String> (I/O errors possible per line).

**JS mental model**

BufReader + lines() is like reading a file line by line in Node with a readline interface.

**Rust example**

```rust
use std::fs::File;
use std::io::{self, BufRead};
use std::path::Path;

fn main() {
    // File hosts.txt must exist in the current path
    if let Ok(lines) = read_lines("./hosts.txt") {
        // Consumes the iterator, returns an (Optional) String
        for line in lines.map_while(Result::ok) {
            println!("{}", line);
        }
    }
}

// The output is wrapped in a Result to allow matching on errors.
// Returns an Iterator to the Reader of the lines of the file.
fn read_lines<P>(filename: P) -> io::Result<io::Lines<io::BufReader<File>>>
where P: AsRef<Path>, {
    let file = File::open(filename)?;
    Ok(io::BufReader::new(file).lines())
}
```

```rust
use std::fs::read_to_string;

fn read_lines(filename: &str) -> Vec<String> {
    read_to_string(filename)
        .unwrap()  // panic on possible file-reading errors
        .lines()  // split the string into an iterator of string slices
        .map(String::from)  // make each slice into a string
        .collect()  // gather them together into a vector
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Each line is Result<String>.
- Handles buffering for you.

_RBE source: `std_misc/file/read_lines.md`_

---

### 21.1.5 Child processes

**What it is**

The `process::Output` struct represents the output of a finished child process,
and the `process::Command` struct is a process builder. (You are encouraged to try the previous example with an incorrect flag passed
to `rustc`).

**JS mental model**

std::process::Command is like child_process.spawn/execFile.

**Rust example**

```rust
use std::process::Command;

fn main() {
    let output = Command::new("rustc")
        .arg("--version")
        .output().unwrap_or_else(|e| {
            panic!("failed to execute process: {}", e)
    });

    if output.status.success() {
        let s = String::from_utf8_lossy(&output.stdout);

        print!("rustc succeeded and stdout was:\n{}", s);
    } else {
        let s = String::from_utf8_lossy(&output.stderr);

        print!("rustc failed and stderr was:\n{}", s);
    }
}
```

**JS equivalent**

```javascript
const { spawn } = require('child_process');
spawn('ls', ['-l']);
```

**Watch out**

- Check status.success().
- Prefer arg() list over shell strings to avoid injection.

_RBE source: `std_misc/process.md`_

---

#### 21.1.5.1 Pipes

**What it is**

You can connect a child process stdout/stderr/stdin as pipes. Read from child.stdout as a file-like Read. This mirrors Node pipes between processes.

**JS mental model**

Piping stdout/stdin to the parent is like spawn with stdio pipes.

**Rust example**

```rust
use std::io::prelude::*;
use std::process::{Command, Stdio};

static PANGRAM: &'static str =
"the quick brown fox jumps over the lazy dog\n";

fn main() {
    // Spawn the `wc` command
    let mut cmd = if cfg!(target_family = "windows") {
        let mut cmd = Command::new("powershell");
        cmd.arg("-Command").arg("$input | Measure-Object -Line -Word -Character");
        cmd
    } else {
        Command::new("wc")
    };
    let process = match cmd
                                .stdin(Stdio::piped())
                                .stdout(Stdio::piped())
                                .spawn() {
        Err(why) => panic!("couldn't spawn wc: {}", why),
        Ok(process) => process,
    };

    // Write a string to the `stdin` of `wc`.
    //
    // `stdin` has type `Option<ChildStdin>`, but since we know this instance
    // must have one, we can directly `unwrap` it.
    match process.stdin.unwrap().write_all(PANGRAM.as_bytes()) {
        Err(why) => panic!("couldn't write to wc stdin: {}", why),
        Ok(_) => println!("sent pangram to wc"),
    }

    // Because `stdin` does not live after the above calls, it is `drop`ed,
    // and the pipe is closed.
    //
    // This is very important, otherwise `wc` wouldn't start processing the
    // input we just sent.

    // The `stdout` field also has type `Option<ChildStdout>` so must be unwrapped.
    let mut s = String::new();
    match process.stdout.unwrap().read_to_string(&mut s) {
        Err(why) => panic!("couldn't read wc stdout: {}", why),
        Ok(_) => print!("wc responded with:\n{}", s),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Stdio::piped() then take() handles.
- Deadlocks possible if you fill pipes without reading — same as Node.

_RBE source: `std_misc/process/pipe.md`_

---

#### 21.1.5.2 Wait

**What it is**

After spawning, wait for completion to get an exit status. Command::output runs and collects stdout/stderr into memory.

**JS mental model**

wait/output waits for the child to finish — like await childPromise.

**Rust example**

```rust
use std::process::Command;

fn main() {
    let mut child = Command::new("sleep").arg("5").spawn().unwrap();
    let _result = child.wait().unwrap();

    println!("reached end of main");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- output() captures stdout/stderr.
- status codes are OS-specific.

_RBE source: `std_misc/process/wait.md`_

---

### 21.1.6 Filesystem Operations

**What it is**

The fs module offers one-shot helpers (read_to_string, write, copy, rename, remove_file, create_dir_all). They are simpler than managing File handles for small tasks.

**JS mental model**

std::fs helpers: read, write, copy, create_dir_all — like fs/promises.

**Rust example**

```rust
use std::fs;
use std::fs::{File, OpenOptions};
use std::io;
use std::io::prelude::*;
#[cfg(target_family = "unix")]
use std::os::unix;
#[cfg(target_family = "windows")]
use std::os::windows;
use std::path::Path;

// A simple implementation of `% cat path`
fn cat(path: &Path) -> io::Result<String> {
    let mut f = File::open(path)?;
    let mut s = String::new();
    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}

// A simple implementation of `% echo s > path`
fn echo(s: &str, path: &Path) -> io::Result<()> {
    let mut f = File::create(path)?;

    f.write_all(s.as_bytes())
}

// A simple implementation of `% touch path` (ignores existing files)
fn touch(path: &Path) -> io::Result<()> {
    match OpenOptions::new().create(true).write(true).open(path) {
        Ok(_) => Ok(()),
        Err(e) => Err(e),
    }
}

fn main() {
    println!("`mkdir a`");
    // Create a directory, returns `io::Result<()>`
    match fs::create_dir("a") {
        Err(why) => println!("! {:?}", why.kind()),
        Ok(_) => {},
    }

    println!("`echo hello > a/b.txt`");
    // The previous match can be simplified using the `unwrap_or_else` method
    echo("hello", &Path::new("a/b.txt")).unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`mkdir -p a/c/d`");
    // Recursively create a directory, returns `io::Result<()>`
    fs::create_dir_all("a/c/d").unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`touch a/c/e.txt`");
    touch(&Path::new("a/c/e.txt")).unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`ln -s ../b.txt a/c/b.txt`");
    // Create a symbolic link, returns `io::Result<()>`
    #[cfg(target_family = "unix")] {
        unix::fs::symlink("../b.txt", "a/c/b.txt").unwrap_or_else(|why| {
            println!("! {:?}", why.kind());
        });
    }
    #[cfg(target_family = "windows")] {
        windows::fs::symlink_file("../b.txt", "a/c/b.txt").unwrap_or_else(|why| {
            println!("! {:?}", why.to_string());
        });
    }

    println!("`cat a/c/b.txt`");
    match cat(&Path::new("a/c/b.txt")) {
        Err(why) => println!("! {:?}", why.kind()),
        Ok(s) => println!("> {}", s),
    }

    println!("`ls a`");
    // Read the contents of a directory, returns `io::Result<Vec<Path>>`
    match fs::read_dir("a") {
        Err(why) => println!("! {:?}", why.kind()),
        Ok(paths) => for path in paths {
            println!("> {:?}", path.unwrap().path());
        },
    }

    println!("`rm a/c/e.txt`");
    // Remove a file, returns `io::Result<()>`
    fs::remove_file("a/c/e.txt").unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`rmdir a/c/d`");
    // Remove an empty directory, returns `io::Result<()>`
    fs::remove_dir("a/c/d").unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });
}
```

```rust
fn cat(path: &Path) -> io::Result<String> {
    let mut f = File::open(path)?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Almost everything returns Result.
- create_dir_all like mkdir -p.

_RBE source: `std_misc/fs.md`_

---

### 21.1.7 Program arguments

**What it is**

Standard Library
The command line arguments can be accessed using `std::env::args`, which
returns an iterator that yields a `String` for each argument:
Crates
Alternatively, there are numerous crates that can provide extra functionality
when creating command-line applications. One of the more popular command line
argument crates being [`clap`]. [`clap`]: https://rust-cli.github.io/book/tutorial/cli-args.html#parsing-cli-arguments-with-clap.

**JS mental model**

std::env::args() is like process.argv.

**Rust example**

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    // The first argument is the path that was used to call the program.
    println!("My path is {}.", args[0]);

    // The rest of the arguments are the passed command line parameters.
    // Call the program like this:
    //   $ ./args arg1 arg2
    println!("I got {:?} arguments: {:?}.", args.len() - 1, &args[1..]);
}
```

**JS equivalent**

```javascript
process.argv.slice(2)
```

**Watch out**

- First arg is the program path.
- For real CLIs use clap.

_RBE source: `std_misc/arg.md`_

---

#### 21.1.7.1 Argument parsing

**What it is**

You can pattern-match on collected args to implement a tiny CLI. This teaches argv handling; larger apps should use a parsing crate.

**JS mental model**

Manual argv matching with match/if — teaching tool; production uses clap/structopt-style parsers.

**Rust example**

```rust
use std::env;

fn increase(number: i32) {
    println!("{}", number + 1);
}

fn decrease(number: i32) {
    println!("{}", number - 1);
}

fn help() {
    println!("usage:
match_args <string>
    Check whether given string is the answer.
match_args {{increase|decrease}} <integer>
    Increase or decrease given integer by one.");
}

fn main() {
    let args: Vec<String> = env::args().collect();

    match args.len() {
        // no arguments passed
        1 => {
            println!("My name is 'match_args'. Try passing some arguments!");
        },
        // one argument passed
        2 => {
            match args[1].parse() {
                Ok(42) => println!("This is the answer!"),
                _ => println!("This is not the answer."),
            }
        },
        // one command and one argument passed
        3 => {
            let cmd = &args[1];
            let num = &args[2];
            // parse the number
            let number: i32 = match num.parse() {
                Ok(n) => {
                    n
                },
                Err(_) => {
                    eprintln!("error: second argument not an integer");
                    help();
                    return;
                },
            };
            // parse the command
            match &cmd[..] {
                "increase" => increase(number),
                "decrease" => decrease(number),
                _ => {
                    eprintln!("error: invalid command");
                    help();
                },
            }
        },
        // all the other cases
        _ => {
            // show a help message
            help();
        }
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Handle --help yourself if manual.
- Invalid args should print usage and exit.

_RBE source: `std_misc/arg/matching.md`_

---

### 21.1.8 Foreign Function Interface

**What it is**

Foreign Function Interface lets Rust call C functions (and expose C ABI). You declare extern "C" blocks and call inside unsafe. Types must match the C side precisely.

**JS mental model**

FFI calls C like Node native addons / ffi-napi — unsafe boundary.

**Rust example**

```rust
use std::fmt;

// this extern block links to the libm library
#[cfg(target_family = "windows")]
#[link(name = "msvcrt")]
extern {
    // this is a foreign function
    // that computes the square root of a single precision complex number
    fn csqrtf(z: Complex) -> Complex;

    fn ccosf(z: Complex) -> Complex;
}
#[cfg(target_family = "unix")]
#[link(name = "m")]
extern {
    // this is a foreign function
    // that computes the square root of a single precision complex number
    fn csqrtf(z: Complex) -> Complex;

    fn ccosf(z: Complex) -> Complex;
}

// Since calling foreign functions is considered unsafe,
// it's common to write safe wrappers around them.
fn cos(z: Complex) -> Complex {
    unsafe { ccosf(z) }
}

fn main() {
    // z = -1 + 0i
    let z = Complex { re: -1., im: 0. };

    // calling a foreign function is an unsafe operation
    let z_sqrt = unsafe { csqrtf(z) };

    println!("the square root of {:?} is {:?}", z, z_sqrt);

    // calling safe API wrapped around unsafe operation
    println!("cos({:?}) = {:?}", z, cos(z));
}

// Minimal implementation of single precision complex numbers
#[repr(C)]
#[derive(Clone, Copy)]
struct Complex {
    re: f32,
    im: f32,
}

impl fmt::Debug for Complex {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        if self.im < 0. {
            write!(f, "{}-{}i", self.re, -self.im)
        } else {
            write!(f, "{}+{}i", self.re, self.im)
        }
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Signatures must match exactly.
- Use libc / bindgen ecosystem.
- All FFI is unsafe.

_RBE source: `std_misc/ffi.md`_

---
