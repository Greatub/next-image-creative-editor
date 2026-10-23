# UI specification

[English](ui-spec.md) | [简体中文](zh-CN/ui-spec.md)

Last updated: 2026-10-23. Status: pending finalization. Tokens: [design/tokens.json](../design/tokens.json).

## 1. Design principles

1. **One location per feature.** Global controls occupy the same location on every page; page-specific controls appear only on that page.
2. **One stable shell.** Header, side panels, footer, and stage padding stay consistent. Switching pages changes panel content without moving the layout.
3. **Keep the photo stage clean.** Only bottom-right view-mode controls and brief status messages (100% zoom, eyedropper sampling, soft proofing) appear over the photo.
4. **Navigation follows the workflow:** Browse → Edit → Album → Print.

## 2. Shared shell

```text
+------------------------- Header: 48px -------------------------+
| NICE | Browse Edit Album Print ... Undo Redo | Primary | Settings |
+-------------+------------------------------------+-------------+
| Left panel  | Photo stage                        | Right panel |
| 300px       | Padding: 32px                      | 300px       |
|             |                  [View controls]   |             |
+-------------+------------------------------------+-------------+
| Footer: 108px | Source: 236px | Filmstrip | Selection/Expand: 96px |
+----------------------------------------------------------------+
```

| Component | Dimensions / rules | Token |
|---|---|---|
| Header | 48px high; left / right padding 16 / 12px | `layout.header` |
| Left and right panels | **Fixed, equal 300px width**; overflow scrolls within the panel | `layout.sidePanel` |
| Footer | 108px high | `layout.footer` |
| Stage padding | 32px; Album / Print bottom padding 72px for pagination | `layout.stagePad`, `layout.stagePadPaged` |
| Primary action | Minimum width 112px; changing text must not push other controls | `layout.primaryMinWidth` |
| View controls | Bottom-right stage corner, 24px inset | `layout.floatInset` |
| Edge hot zone | 6px | `layout.hotZone` |

**Layout-stability checklist**, required for interface changes:

- Do not add a toolbar above the photo stage.
- Browse and Edit must match photo position and size: 32px padding and the same fit behavior.
- Keep page-specific controls in side panels.
- Keep the three footer sections stable; Print also uses a 236px source section.
- Place Album and Print pagination identically: bottom center, 24px inset.
- Keep primary-action width fixed.

## 3. View modes

| Mode | Header | Left | Right | Footer |
|---|---|---|---|---|
| Standard | Fixed | Fixed | Fixed | Fixed |
| Focus | Auto-hide | Auto-hide | Auto-hide | Fixed |
| Immersive | Auto-hide | Auto-hide | Auto-hide | Auto-hide |

- View mode is independent of window / fullscreen and supports every combination.
- No per-panel pins; only these three modes.
- Auto-hidden panels slide out as overlays in 160ms when the pointer reaches the matching 6px edge zone. Retract on leaving without changing the mode.
- Bottom-right controls contain Standard / Focus / Immersive buttons, with icons showing visible panels, and window / fullscreen toggle. Fade in on movement (300ms), fade out after 2 seconds idle. Do not fade while hovered. Hide the pointer too in Immersive.
- Keep the header visible while Settings is open, including in auto-hide modes.
- P1: Tab cycles modes, F fullscreen, Esc exits fullscreen and returns to Standard. Resolve edge conflicts with the macOS fullscreen menu bar / Windows taskbar using about 0.3s reveal delay and inset hot zones.

## 4. Header

`NICE logo | Navigation (4 pages) ... flexible space ... Undo · Redo | Primary action | Settings`

| Page | Primary action |
|---|---|
| Browse | Export… |
| Edit | Export… |
| Album | Export print files |
| Print | Print, or Save as PDF when the PDF printer is selected |

Settings currently contains theme selection (Dark / Neutral Gray). P1 adds compatibility layer, monitor ICC, default directory, and shortcuts.

## 5. Shared footer

- **Left, 236px:** current source icon + name, **read-only**, with the hint “Choose sources in Browse's left panel.” Second line: source type and sort / filter summary. Third line: “n / total” and multiselect hint; Album replaces this line with Show used photos.
- **Center:** filmstrip, 100×66px thumbnails with 8px gaps. Badges: file type, edited (yellow dot), selected (check), rejected (red label, photo darkened 40%), and Album's Used / page number.
- **Right, 96px:** “n selected” and Expand / Collapse. Expanded thumbnails become a grid inside the stage; its header shows only source, photo count, Select all, and Clear selection.
- Click selects the current photo; Ctrl/⌘ click adds/removes selections; Shift click selects a range.
- On Album, show photos in the current album **not yet placed in the layout** by default.

## 6. Page layouts

### 6.1 Browse

- **Left, top to bottom:** memory-card card (Import…) · search · folders · albums (+ Album / + Smart album, tree, ＋ on each editable row, drag targets) · classification hint / current album properties · filters and sorting (stars, edited only, hide rejected, capture time / rating, Stack NEF+JPEG).
- **Stage:** current photo; double-click for 100%.
- **Right:** filename and dimensions · NEF / JPEG switch when stacked · five rating buttons · color label (none / red / yellow / green / blue / purple) · Reject · shortcut hints · single-line edit status · read-only album membership · capture metadata.

### 6.2 Edit

- **Left:** Style / Film tabs; three-column preset grid; strength (Grain under Film); parameter card with save state, `NKSC_PARAM/…nksc` path, changed items, Save as preset, and Copy to selected n.
- **Stage:** edit preview; double-click for 100%; eyedropper uses a crosshair and temporary top hint; clipping overlays; “Original” at top left during before/after comparison.
- **Right:** fixed top section with histogram, shadow ◣ / highlight ◢ clipping buttons, before / after, and Adjustments / Negative conversion tabs; scrollable content below.
- **Adjustments:** ① Geometry → ② Exposure and white balance (Restorable in NX Studio) → ③ Tone → ④ Color → ⑤ Detail. Each section has Reset; double-click sliders to reset individually.
- **Negative conversion:** label “NICE only · Not visible in NX Studio”; see ED-07 in [product.md](product.md).
- UI order matches processing order; see [architecture §4](architecture.md#4-processing-pipeline).

### 6.3 Album

- **Left:** two-column page thumbnails, proportional to finished size; Delete spread / + Spread; size and page count.
- **Stage:** current spread on white paper with shadow; dashed empty photo frames accepting click-to-place and drag; editable captions; guide overlays; bottom-center pagination.
- **Right:** three-column template previews · guide toggles (red dashed bleed / black solid trim / blue dashed safe area / gray dashed gutter / trimmed preview) · layout settings (finished size, 3mm bleed, 5mm safe distance, binding, provider preset) · preflight (green = pass, orange = attention).

### 6.4 Print

- **Left, Layout:** paper, orientation, per-page layout. For ordinary photos: fit mode, margins, printed information. For ID photos: specifications, portrait position (individual / full-sheet preview, zoom, vertical position, auto-align, status), background, and specification source notes.
- **Stage:** page preview, with paper/color simulation during soft proofing. Individual ID adjustment shows top-of-head and chin guides in red, eye band in blue, and dashed gray centerline. Pagination at the bottom.
- **Right, Output:** printer including Save as PDF, paper type, color management (ICC, perceptual / relative colorimetric, soft proof), copies, summary, and “Print content = filmstrip selection.”

## 7. Dialogs

- Shared style: 55% black overlay; 600px-wide panel with 12px radius; rows use a 112px label and segmented choice; Cancel / primary action at the bottom.
- Import on Browse; Export on Browse / Edit; Export print files on Album. Printing uses the system print dialog.

## 8. Feature location table

| Feature | Only location |
|---|---|
| Source selection, search, filters, sorting, NEF+JPEG stacking | Browse left panel |
| Album creation, hierarchy, smart rules, keyword settings; add/remove photos | Browse left panel |
| Ratings, color labels, rejection | Browse right panel, plus shortcuts |
| Editing parameters, styles, film profiles, negative conversion | Edit side panels |
| Page order, add/delete pages | Album left panel |
| Place photos in layouts | Album: available filmstrip photos → photo frames |
| Print layout / ID photos | Print left panel |
| Printer, color management, soft proofing | Print right panel |
| Theme and preferences | Settings |
| View mode and fullscreen | Bottom-right floating stage controls |
| Export / Export print files / Print | Header primary action |

## 9. Interactions and shortcuts

| Action | Interaction |
|---|---|
| Zoom | Double-click: Fit ↔ 100%; wheel zoom remains to be implemented for P0 |
| Rate / clear rating / reject | 1–5 / 0 / X |
| Multiselect / range selection | Ctrl/⌘ click / Shift click |
| Reset one parameter | Double-click slider |
| View mode / fullscreen | Tab cycles, F fullscreen, Esc exits (P1) |

## 10. Accessibility

- Text contrast ≥4.5:1 in both themes, including small muted text.
- Clickable elements use native `button` / `a` / `input`; icon-only controls need readable names.
- Every drag-and-drop action has a click equivalent, such as album ＋ and click-to-place in empty frames.
- Distinguish states by luminance as well as hue; clipping overlays use red / blue.
- Touch targets ≥28px in dense desktop panels; primary controls ≥32px.
