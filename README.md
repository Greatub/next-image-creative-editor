# NICE · Next Image Creative Editor

[English](README.md) | [简体中文](docs/zh-CN/README.md)

A desktop photo application for Nikon photographers: **Browse → Edit → Album → Print**.

The core commitment is to keep editing parameters separate from original files. **Original NEF/JPEG files are never modified.** Nikon-compatible parameters are intended to be stored in `NKSC_PARAM/*.nksc` for use in NX Studio / Capture NX-D; third-party read/write compatibility still requires validation ([Q-002](docs/open-questions.md)).

- **Status:** UI concept, pending finalization. Last updated: 2026-10-23.
- **Platforms:** macOS and Windows desktop. Both `.icns` and `.ico` assets are provided.
- **Technology:** not selected yet; see [open questions](docs/open-questions.md). This repository does not contain an application implementation or a standalone runnable demo.

## Documentation language

English is the primary language of repository documentation; Simplified Chinese is secondary. Full Chinese translations live in [docs/zh-CN](docs/zh-CN/README.md). Maintain both versions together, with English as the maintenance baseline ([D-026](docs/decisions.md)). The existing Chinese design prototypes remain reference material; this documentation policy does not decide the product's interface language.

## Repository map

| Path | Contents | Maintainer |
|---|---|---|
| [AGENTS.md](AGENTS.md) | Required collaboration rules, source priority, and change process | Project owner |
| [CLAUDE.md](CLAUDE.md) | Entry point for Claude Code, pointing to the shared rules | — |
| [docs/product.md](docs/product.md) | Positioning, users, v1 scope, and requirements for all four pages | Product |
| [docs/ui-spec.md](docs/ui-spec.md) | Shared shell, dimensions, view modes, page layouts, interactions, and shortcuts | Design |
| [docs/architecture.md](docs/architecture.md) | L1/L2/L3 parameters, sidecars, processing pipeline, and technical validation | Architecture |
| [docs/data-model.md](docs/data-model.md) | Photos, NEF+JPEG pairs, albums, photo books, ID photo specifications, and jobs | Architecture |
| [docs/decisions.md](docs/decisions.md) | Numbered decisions; confirmed decisions must not be silently overturned | Project owner |
| [docs/open-questions.md](docs/open-questions.md) | Pending decisions and validation work | Everyone |
| [docs/roadmap.md](docs/roadmap.md) | v1 (P0), v1.x (P1), and later scope | Product |
| [docs/glossary.md](docs/glossary.md) | English and Chinese terminology with code identifiers | Everyone |
| [design/tokens.json](design/tokens.json) | Two themes, typography, dimensions, and motion tokens | Design |
| [design/prototype/](design/prototype/README.md) | Local design snapshots and their limitations | Design |
| [App Icon/](App%20Icon/) | Final SVG, ICNS, ICO, and PNG assets; do not modify | Brand |
| [SVG Icon/](SVG%20Icon/) | Alternative icon designs; do not modify | Brand |

## Repository and collaboration

- Public GitHub repository: [Greatub/next-image-creative-editor](https://github.com/Greatub/next-image-creative-editor).
- `main` holds the shared baseline. Make changes on a separate feature branch and merge through a pull request.
- Inspect `git status` and staged changes before committing. Do not commit local environment configuration, credentials, or real user photos. `.gitignore` excludes system files and common local configuration.
- There is no `package.json` yet. `pnpm lint`, `pnpm typecheck`, and `pnpm build` are not applicable at this stage. Add development and validation commands after selecting the technology stack.

## Suggested reading order

1. [Collaboration rules](AGENTS.md).
2. [Decisions](docs/decisions.md): understand what has already been agreed.
3. [Product](docs/product.md) → [UI specification](docs/ui-spec.md) → [Architecture](docs/architecture.md) → [Data model](docs/data-model.md).
4. [Open questions](docs/open-questions.md) and [Roadmap](docs/roadmap.md).
5. [Prototype notes](design/prototype/README.md) when inspecting the interface concepts.

## Core principles

One location per feature · One stable shell across four pages · A clean photo stage · Navigation follows the workflow · Original files remain read-only.
