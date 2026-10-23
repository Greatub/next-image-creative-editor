# AGENTS.md · Collaboration rules

[English](AGENTS.md) | [简体中文](docs/zh-CN/AGENTS.md)

Last updated: 2026-10-23.

Every agent and human contributor working on NICE must read this document before starting.

## 1. Sources of truth

Resolve source priority in this order:

1. [docs/decisions.md](docs/decisions.md): confirmed decisions have the highest priority.
2. [Product](docs/product.md), [UI specification](docs/ui-spec.md), [Architecture](docs/architecture.md), and [Data model](docs/data-model.md).
3. [design/tokens.json](design/tokens.json).
4. [design/prototype/](design/prototype/README.md): use only to understand design intent. Example data and simplified algorithms, such as CSS filters for color adjustments, do not specify the implementation.
5. Chat history and verbal descriptions.

If documents contradict each other or the requested task, do not choose a side yourself. Record the conflict in `docs/open-questions.md`, report it, and stop at that point. Explicit owner decisions must be recorded with their replacement relationship; do not erase historical decisions. D-026 records the owner's English-first documentation request and supersedes D-025's language rule.

## 2. Product constraints

- **Original files are read-only.** Never modify original NEF / JPEG / HEIF files. Store edits in parameter files or newly exported files.
- **Parameter layers** (see `docs/architecture.md`):
  - L1: Nikon-compatible parameters → `NKSC_PARAM/<filename>.nksc`, restorable in NX Studio, subject to compatibility validation.
  - L2: NICE-only parameters → NICE sidecars / database.
  - L3: effects baked into new files during export.
  - Every new feature must declare its layer and show the applicable interface label: “Restorable in NX Studio” / “NICE only” (Chinese: “NX Studio 可还原” / “仅 NICE”).
- **One location per feature.** Check the feature location table in `docs/ui-spec.md` before adding an entry point. Reuse existing locations.
- **Stable layout.** All four pages share a 48px header, two 300px side panels, a 108px footer, and 32px photo-stage padding. Add content inside panels without changing shell dimensions.
- **Choose sources only on Browse.** The footer's source indicator is display-only and cannot be clicked.
- **Keep the photo stage clean.** Only the bottom-right view-mode control and temporary status messages, such as a 100% zoom indicator, may appear over the photo area.

## 3. Workflow

1. Read this file, `docs/decisions.md`, and the relevant specification sections.
2. Before adding or changing product behavior, raise a question in `docs/open-questions.md`. Implement after the project owner confirms it and the decision is recorded in `docs/decisions.md`.
3. After implementation, update affected documentation and its “Last updated” date. Record any new technical decision about libraries, formats, or directory structure in `docs/decisions.md` as **Proposed**, pending confirmation.
4. Do not delete other contributors' files or decision records. Mark superseded material and state the reason and replacement.
5. Check the branch and working tree before changes. Do not develop directly on `main`; use a separate feature branch. Explain the plan first and report changed files, validation, and risks afterward.

## 4. Documentation and code conventions

- **English first, Simplified Chinese second** (D-026). Default documents are English; complete Chinese translations live in `docs/zh-CN/`. Link corresponding versions and update both together. English is the maintenance baseline. Code, identifiers, filenames, and commit messages use English.
- Use `docs/glossary.md` consistently. In Chinese, use “相册”, not “影集 / 标签”, and “显示方式” for Standard / Focus / Immersive.
- Use `D-001` for decisions, `Q-001` for questions, and requirement IDs from `docs/product.md`, such as `BR-03`.
- Reference token names in `design/tokens.json` for colors, dimensions, and motion durations instead of hardcoding values.
- The technology stack is undecided (Q-001). Do not create `src/` or introduce a framework before it is agreed.
- Do not commit API keys, tokens, private configuration, or real user data. Use `.env` / `.env.local` for local environment variables. Keep browser-extension permissions minimal.
- Do not restructure the whole project, remove existing functionality, or add unrelated boilerplate without an explicit request. Explain any proposed framework's purpose before introducing it.

## 5. Brand assets

- `App Icon/` contains final icons; `SVG Icon/` contains alternatives. **Do not modify, move, or re-export them.** Request new sizes in `docs/open-questions.md`.
- The product is **NICE**, short for **Next Image Creative Editor**. Always use uppercase `NICE` in the interface. Do not change the logo, brand colors, or product name unless explicitly requested.

## 6. Acceptance checklist

- [ ] Original photo files were not written to.
- [ ] New parameters declare L1 / L2 / L3 and carry the applicable interface label.
- [ ] No duplicate entry points were added; check the feature location table.
- [ ] Shell dimensions are unchanged; switching between the four pages causes no layout shift.
- [ ] Text contrast is at least 4.5:1 in both Dark and Neutral Gray themes.
- [ ] Clickable elements are real buttons, links, or form controls with readable names.
- [ ] Every drag-and-drop action has an equivalent click action.
- [ ] English and Chinese documentation are synchronized.

Run `pnpm lint`, `pnpm typecheck`, and `pnpm build` when available. If they cannot run, explain why and provide applicable manual checks. At the current documentation-only stage there is no `package.json` or application build configuration.
