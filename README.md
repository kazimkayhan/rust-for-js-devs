# Rust for JS Devs

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

A complete, open-source **Rust zero-to-hero** tutorial written for people who already know **JavaScript (ES6+)**.

Aligned with the official [Rust by Example](https://doc.rust-lang.org/rust-by-example/) curriculum — **every nested topic** covered in simple words, with clear examples.

## 📖 Read Online

**→ [https://kazimkayhan.github.io/rust-for-js-devs/](https://kazimkayhan.github.io/rust-for-js-devs/)**

The tutorial is hosted as an interactive mdBook site with:
- **Runnable Rust examples** via the Rust Playground (click ▶ on code blocks!)
- Sidebar navigation through all 26 chapters
- Search functionality
- Mobile-friendly design

You can also read the [single-file GUIDE.md](./GUIDE.md) version.

## Features

Each topic includes:

1. Plain English (simple words)
2. A JavaScript mental model / analogy
3. Rust examples (runnable in the browser on the live site!)
4. JS equivalents when useful
5. Common pitfalls for JS developers

## Repo layout

| File/Directory | What it is |
|---|---|
| [`src/`](./src/) | mdBook source chapters (structured tutorial) |
| [`GUIDE.md`](./GUIDE.md) | Single-file version of the full tutorial |
| [`book.toml`](./book.toml) | mdBook configuration with Rust Playground integration |
| [`COVERAGE.md`](./COVERAGE.md) | Checklist: 197/197 RBE leaf topics |
| [`RBE_SUMMARY.md`](./RBE_SUMMARY.md) | Official Rust by Example table of contents |
| [`LICENSE`](./LICENSE) | MIT |

## Building locally

### Prerequisites

Install [mdBook](https://rust-lang.github.io/mdBook/):

```bash
cargo install mdbook
```

### Build and serve

```bash
# Clone the repository
git clone https://github.com/kazimkayhan/rust-for-js-devs.git
cd rust-for-js-devs

# Serve locally at http://localhost:3000
mdbook serve --open
```

Or just build without serving:

```bash
mdbook build
# Output will be in ./book/
```

## Official companions

- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [The Rust Book](https://doc.rust-lang.org/book/)
- [Install with rustup](https://rustup.rs)

## How to use

1. Install Rust with `rustup`.
2. Visit the [live site](https://kazimkayhan.github.io/rust-for-js-devs/) and work through each chapter.
3. Run the examples directly in your browser using the Playground buttons.
4. Create a practice crate (`cargo new rust_playground`) and type the examples yourself.
5. Spend extra time on **ownership, borrowing, and lifetimes** (chapter 16).
6. Follow the practice checklist in chapter 25.

## Contributing

PRs welcome — fix typos, improve examples, or clarify a section for JS newcomers.

To contribute to the book content, edit the files in the `src/` directory and test your changes with `mdbook serve`.

## License

MIT — see [`LICENSE`](./LICENSE).

Tutorial text is original teaching material structured around the public Rust by Example curriculum.
Rust and Rust by Example are projects of the Rust Project / Rust Foundation.
