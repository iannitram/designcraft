# DesignCraft code map (our fork)

Ours, not upstream's. Built 2026-10-26 from the Cargo workspace (`crates/*`, `apps/*`, `xtask`) and each crate's `Cargo.toml` description; line counts are Rust lines on `main` that day. Upstream's own architecture notes: [`docs/architecture.md`](architecture.md) where present, and `AGENTS.md`. When a crate is added or split, update this table.

## Crates

| Path | What it owns | Uses (internal) | Platforms | Size |
|---|---|---|---|---|
| `crates/color` | DesignCraft color | — | 1 file(s) with per-OS or wasm code | ~3,264 |
| `crates/compose` | DesignCraft text composition: paragraph composer (Knuth–Plass), hyphenation, justification, columns, threading, wrap | `color`, `doc`, `fonts`, `geom` | portable | ~22,575 |
| `crates/doc` | DesignCraft document model: spreads, pages, parents, layers, frames, stories, styles, swatches | `color`, `geom` | 2 file(s) with per-OS or wasm code | ~16,414 |
| `crates/engine` | DesignCraft engine: session, documents, history, selection, command registry, tool hosting | `color`, `compose`, `doc`, `epub`, `fonts`, `format`, `geom`, `idml`, `images`, `pdf`, `pptx`, `render`, `textimport`, `tools` | 25 file(s) with per-OS or wasm code | ~67,041 |
| `crates/epub` | DesignCraft EPUB 3 (reflowable) export: stories in reading order, CSS from styles, images | `color`, `doc` | portable | ~2,763 |
| `crates/fonts` | DesignCraft fonts: font database (bundled OFL + system), metrics, glyph outlines, shaping | — | 5 file(s) with per-OS or wasm code | ~5,344 |
| `crates/format` | DesignCraft native file format: zip with document.json and embedded assets; versioned | `doc`, `images` | portable | ~1,142 |
| `crates/geom` | DesignCraft geometry: anchor paths, shapes, units and measurements, hit testing | — | portable | ~2,865 |
| `crates/idml` | DesignCraft IDML (InDesign Markup Language) import and export | `color`, `compose`, `doc`, `geom`, `images` | portable | ~16,163 |
| `crates/images` | DesignCraft placed-graphic decoding: raster formats (PNG, JPEG, GIF, WebP, TIFF, BMP, PSD) and SVG | `color`, `fonts` | portable | ~3,285 |
| `crates/mcp` | DesignCraft MCP server: JSON-RPC 2.0 over stdio, driving a running app or a headless session | `engine`, `geom`, `render`, `tools` | 1 file(s) with per-OS or wasm code | ~2,570 |
| `crates/pdf` | DesignCraft PDF export (krilla): real text with embedded font subsets, CMYK/spot colour, bleed and printer's marks | `color`, `compose`, `doc`, `fonts`, `geom`, `images` | 1 file(s) with per-OS or wasm code | ~9,752 |
| `crates/pptx` | DesignCraft PowerPoint (PPTX) export: pages as slides of editable shapes, pictures and text | `color`, `compose`, `doc`, `fonts`, `geom`, `images` | portable | ~2,647 |
| `crates/render` | DesignCraft renderer: spreads, pages, frames, composed text and images to pixels (vello_cpu) | `color`, `compose`, `doc`, `fonts`, `geom`, `images` | 1 file(s) with per-OS or wasm code | ~6,957 |
| `crates/textimport` | DesignCraft text import: plain text, Word (.docx) and RTF into stories | `doc` | portable | ~3,022 |
| `crates/tools` | DesignCraft tools: pointer events in, commands and overlays out (Selection, Direct Selection, frames, Type, Hand, Zoom…) | `color`, `compose`, `doc`, `geom` | portable | ~9,394 |
| `crates/ui-egui` | DesignCraft egui frontend (InDesign-style UI); swappable — nothing below depends on it | `color`, `compose`, `doc`, `engine`, `fonts`, `geom`, `idml`, `render`, `tools` | 18 file(s) with per-OS or wasm code | ~89,615 |
| `apps/designcraft` | (no description in Cargo.toml) | `engine`, `ui-egui` | 4 file(s) with per-OS or wasm code | ~4,193 |
| `apps/designcraft-cli` | (no description in Cargo.toml) | `color`, `compose`, `doc`, `engine`, `format`, `geom`, `mcp`, `pdf`, `render` | 1 file(s) with per-OS or wasm code | ~1,833 |
| `apps/designcraft-web` | DesignCraft in the browser (eframe web runner, WebGPU with WebGL2 fallback) | `engine`, `format`, `ui-egui` | 1 file(s) with per-OS or wasm code | ~712 |
| `xtask` | Build and check tasks (`cargo xtask ci`, parity reports) | — | portable | — |

## How they interlink

The engine owns the session, undo and the command registry; every user action is a command. The `ui-egui` front end sits on top and is swappable: nothing below it depends on it. The desktop, CLI and web apps are thin shells over the engine and the front end. The MCP crate drives the engine headless or through the running app.

- `color` is used by `compose`, `designcraft-cli`, `doc`, `engine`, `epub`, `idml`, `images`, `pdf`, `pptx`, `render`, `tools`, `ui-egui`.
- `compose` is used by `designcraft-cli`, `engine`, `idml`, `pdf`, `pptx`, `render`, `tools`, `ui-egui`.
- `doc` is used by `compose`, `designcraft-cli`, `engine`, `epub`, `format`, `idml`, `pdf`, `pptx`, `render`, `textimport`, `tools`, `ui-egui`.
- `engine` is used by `designcraft`, `designcraft-cli`, `designcraft-web`, `mcp`, `ui-egui`.
- `epub` is used by `engine`.
- `fonts` is used by `compose`, `engine`, `images`, `pdf`, `pptx`, `render`, `ui-egui`.
- `format` is used by `designcraft-cli`, `designcraft-web`, `engine`.
- `geom` is used by `compose`, `designcraft-cli`, `doc`, `engine`, `idml`, `mcp`, `pdf`, `pptx`, `render`, `tools`, `ui-egui`.
- `idml` is used by `engine`, `ui-egui`.
- `images` is used by `engine`, `format`, `idml`, `pdf`, `pptx`, `render`.
- `mcp` is used by `designcraft-cli`.
- `pdf` is used by `designcraft-cli`, `engine`.
- `pptx` is used by `engine`.
- `render` is used by `designcraft-cli`, `engine`, `mcp`, `ui-egui`.
- `textimport` is used by `engine`.
- `tools` is used by `engine`, `mcp`, `ui-egui`.
- `ui-egui` is used by `designcraft`, `designcraft-web`.

## Mac, PC and Linux (R04)

- Ships for macOS, Windows and Linux (plus FreeBSD and the web) from upstream's release workflow.
- Per-OS code sits mostly in the engine, the `ui-egui` front end and the desktop app (the "Platforms" column counts files with `cfg(target_os…)`, `cfg(windows|unix)` or wasm code). Everything else is portable Rust.
- Upstream CI (`ci.yml`) calls `storytold/craft-common-actions` `gate.yml` with its default `os` input, `["ubuntu-24.04"]`: pull requests are tested on Linux only. Release, FreeBSD, Nix and Windows ARM64 have their own workflows.
- A build issue that touches per-OS code carries a `platform:*` label and says which OS it was built and checked on.
