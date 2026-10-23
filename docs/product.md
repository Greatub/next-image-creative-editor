# Product requirements (PRD)

[English](product.md) | [简体中文](zh-CN/product.md)

Last updated: 2026-10-23. Status: pending finalization.

## 1. Positioning

- **Name:** NICE (Next Image Creative Editor).
- **Users:** Nikon photographers, including film digitization with adapters such as the ES-2. Dedicated support for other brands' RAW formats is out of scope.
- **Core value:**
  1. Editing parameters remain separate; original files are never modified.
  2. Nikon-compatible parameters go into `.nksc` for NX Studio / Capture NX-D, subject to interoperability validation (see [architecture](architecture.md) and Q-002).
  3. Culling, editing, photo book layout, and printing in one application.
- **Interface principles:** one location per feature; one stable shell across four pages; a clean photo stage; navigation follows the workflow.

## 2. Information architecture

The top navigation follows the workflow:

| Page | Purpose | Left panel | Center | Right panel | Header primary action |
|---|---|---|---|---|---|
| Browse | Import, source selection, culling, organization | Import · search · folders · album management · filters and sorting | Large photo | Photo information | Export… |
| Edit | Photo editing | Style / Film tabs · parameters | Edit preview | Histogram · Adjustments / Negative conversion tabs | Export… |
| Album | Lay out an album as a photo book | Page thumbnails | Current spread | Templates · guides · layout settings · preflight | Export print files |
| Print | Printer output | Layout, including ID photos | Page preview | Printer / PDF · color management · copies · summary | Print / Save as PDF |

Shared: header (undo, redo, primary action, Settings), footer filmstrip, and bottom-right view-mode controls.

## 3. Functional requirements

P0 = required for v1; P1 = v1.x. **Designed** means shown in the [design prototypes](../design/prototype/README.md), not implemented.

### 3.1 Global (GL)

| ID | Requirement | Priority | Design status |
|---|---|---|---|
| GL-01 | Top navigation: Browse / Edit / Album / Print | P0 | Designed |
| GL-02 | Shared header: logo · navigation · undo · redo · page primary action (fixed width ≥112px) · Settings ⚙ | P0 | Designed |
| GL-03 | Dark / Neutral Gray themes, switched in Settings | P0 | Designed |
| GL-04 | Standard (all panels fixed) / Focus (only footer fixed) / Immersive (all auto-hide), independent of window / fullscreen | P0 | Designed |
| GL-05 | Bottom-right floating view controls appear on mouse movement, fade after 2 seconds idle; hide the pointer too in Immersive | P0 | Designed |
| GL-06 | Auto-hidden panels float out when the pointer reaches the window edge and retract on leaving | P0 | Designed |
| GL-07 | Shared footer filmstrip; click / Ctrl/⌘ multiselect / Shift range selection; Expand switches to a grid | P0 | Designed |
| GL-08 | Footer source indicator displays the current folder / album and is not clickable; choose sources only on Browse | P0 | Designed |
| GL-09 | Export dialog: JPEG / 16-bit TIFF / HEIF, dimensions, color space, sharpening, metadata, filename rules | P0 | Designed |
| GL-10 | Double-click toggles Fit / 100%; wheel zoom; temporary bottom-left zoom indicator | P0 | Designed: double-click |
| GL-11 | Complete Settings: compatibility layer, monitor ICC, default directory, shortcuts | P1 | — |
| GL-12 | Shortcuts: Tab cycles view modes, F fullscreen, Esc exits fullscreen and returns to Standard | P1 | — |

### 3.2 Browse (BR)

| ID | Requirement | Priority | Design status |
|---|---|---|---|
| BR-01 | Detect memory cards; import dialog for scope, destination, naming, NEF+JPEG handling, simultaneous backup, and album membership; copy only, never modify card files | P0 | Designed |
| BR-02 | Search filenames in the current source | P0 | Designed |
| BR-03 | Folder list; click to switch source | P0 | Designed |
| BR-04 | Album tree with nesting, smart and temporary albums; create albums; click to switch source | P0 | Designed |
| BR-05 | Add filmstrip photos to albums by dragging or selecting photos and clicking the album row's ＋ | P0 | Designed |
| BR-06 | Current album properties: Write photo keywords toggle with hierarchy path, remove selected photos, smart-rule explanation | P0 | Designed |
| BR-07 | Filter by stars, edited only, hide rejected; sort by capture time / rating; Stack NEF+JPEG display option | P0 | Designed |
| BR-08 | Photo information: filename, dimensions, NEF/JPEG switch, rating, color label, rejection, single-line edit status, read-only album membership, capture metadata | P0 | Designed |
| BR-09 | Editable rating / color label / rejection; shortcuts 1–5 / 0 / X | P0 | Designed: click |
| BR-10 | Side-by-side comparison of two photos | P1 | — |
| BR-11 | IPTC title, description, and copyright editing; source for album / print captions | P1 | — |
| BR-12 | Photo status: Unprocessed / Editing / Complete | Pending | — |

### 3.3 Edit (ED)

| ID | Requirement | Priority | Design status |
|---|---|---|---|
| ED-01 | Left Style tab: 12 style presets with strength | P0 | Designed |
| ED-02 | Left Film tab: film profiles (portrait, landscape, everyday, black-and-white negatives, reversal film, etc.) and grain | P0 | Designed |
| ED-03 | Parameter card: file path, autosave state, changed items, Copy to selected, Save as preset | P0 | Designed |
| ED-04 | Fixed histogram at top right, before / after comparison, clickable shadow / highlight clipping overlays | P0 | Designed |
| ED-05 | Right Adjustments tab: ① Geometry (lens corrections, straighten, crop ratio, grids) ② Exposure and white balance (presets, eyedropper, Picture Control, exposure, temperature, tint) ③ Tone ④ Color ⑤ Detail | P0 | Designed |
| ED-06 | Grid: none / thirds / golden ratio / fine | P0 | Designed |
| ED-07 | Right Negative conversion tab: mark as film scan, type (color negative / B&W negative / reversal), film-base sampling shared by roll, density, three color-balance pairs, dust removal, auto-crop borders, view original negative, auto levels, Sync to roll | P0 | Designed |
| ED-08 | Reset each section; double-click a slider to reset one value | P0 | Designed |
| ED-09 | My presets group; Reset all | P1 | — |
| ED-10 | History and snapshots | P1 | — |
| ED-11 | Spot removal / local retouching | P1 | — |
| ED-12 | Editing workflow templates with configurable steps and defaults | Pending | — |

### 3.4 Album (AL)

| ID | Requirement | Priority | Design status |
|---|---|---|---|
| AL-01 | Front cover, spread, and back cover thumbnails; click to switch; add / delete spreads (covers cannot be deleted); drag to reorder | P0 | Designed; drag described only |
| AL-02 | Current spread in the center; toggles for bleed / trim / safe area / gutter / trimmed preview | P0 | Designed |
| AL-03 | Filmstrip shows available photos not yet placed; Show used photos toggle with page labels | P0 | Designed |
| AL-04 | Place photos by dragging into a frame or selecting then clicking an empty frame; remove placed photos from frames | P0 | Designed |
| AL-05 | Built-in templates and a custom-template entry point; preserve corresponding photos when switching templates | P0 | Designed: 3 built-in |
| AL-06 | Edit captions directly in the layout | P0 | Designed |
| AL-07 | Finished size: 20×20 / 21×28 portrait / 28×21 landscape / 30×30 cm; recalculate layouts and guides | P0 | Designed |
| AL-08 | Preflight: empty frames, edge-to-edge bleed, gutter, effective resolution, text safe area | P0 | Designed |
| AL-09 | Export print files: print-ready PDF/X-4 / per-page JPEG / TIFF, crop marks, bleed, RGB+ICC / CMYK, print-provider presets | P0 | Designed |
| AL-10 | Standard file output only; no online photo book provider integration | — | D-014 |
| AL-11 | Full template system: about 40 page templates / 3–4 collections, editor, import/export | P1 | — |
| AL-12 | Slideshows through an Album Presentation view | Later | D-020 |

### 3.5 Print (PR)

| ID | Requirement | Priority | Design status |
|---|---|---|---|
| PR-01 | Paper, orientation, per-page layout (single / 2 / 4 / contact sheet / ID photos), fit mode, margins, printed information | P0 | Designed |
| PR-02 | Print content equals filmstrip selection | P0 | Designed |
| PR-03 | Printer including Save as PDF, paper type, color management (ICC + perceptual / relative colorimetric), copies, summary | P0 | Designed |
| PR-04 | Soft proof print appearance using printer ICC | P0 | Designed |
| PR-05 | Common international ID specifications (concept examples: Schengen/UK/Japan 35×45 mm, US 2×2 in, China visa 33×48 mm, Canada 50×70 mm); white / light blue / original background; auto-fill paper and draw crop marks. Verify actual specifications under Q-008 | P0 | Designed |
| PR-06 | ID portrait adjustment: individual view with top-of-head / chin / eye band / center guides; zoom, vertical position, auto alignment, alignment status; full-sheet preview | P0 | Designed |
| PR-07 | Digital ID photo export with required pixels and file size | P1 | — |
| PR-08 | ID background replacement using local portrait segmentation | P1 | — |

## 4. Out of scope for this version

- Slideshows (D-020; planned as Album's Presentation view).
- Direct ordering from online photo book providers (D-014).
- Dedicated RAW support for non-Nikon brands (D-004).

## 5. Acceptance

Use [AGENTS.md §6](../AGENTS.md#6-acceptance-checklist) and the layout-stability checklist in [ui-spec.md](ui-spec.md).
