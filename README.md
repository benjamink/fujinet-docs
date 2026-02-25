# FujiNet Documentation

This repo builds the FujiNet documentation site at http://tech.superk.org/fujinet-docs/index.html.

## Developing Locally

### Prerequisites

Install [mdbook](https://github.com/rust-lang/mdBook/releases) and [mdbook-mermaid](https://github.com/badboy/mdbook-mermaid/releases) — grab the latest binaries for your platform and put them somewhere on your `$PATH`.

On macOS with Homebrew:
```
brew install mdbook
cargo install mdbook-mermaid
```

On Linux with cargo:
```
cargo install mdbook mdbook-mermaid
```

### First-time setup

Run this once from the repo root to install the Mermaid JS files into `src/`:
```
mdbook-mermaid install
```

### Serving locally

```
mdbook serve --open
```

This starts a local server at `http://localhost:3000` and opens it in your browser. The book rebuilds automatically whenever you save a file.

## Contributing

1. Fork this repository and create a branch for your changes.
2. Add or edit Markdown files under `src/`. The directory structure mirrors the sidebar.
3. If you add a new page, add a corresponding entry to [`src/SUMMARY.md`](src/SUMMARY.md) — that file controls the sidebar and build order.
4. Preview your changes locally with `mdbook serve --open`.
5. Open a pull request against `main`. The site deploys automatically on merge.

### Adding a new page

- Create a `.md` file in the appropriate subdirectory under `src/`.
- Add a line for it in `src/SUMMARY.md` at the right indentation level.
- Link to it from any related pages where a cross-reference would help readers.
