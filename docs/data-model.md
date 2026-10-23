# Data model (concept)

[English](data-model.md) | [简体中文](zh-CN/data-model.md)

Last updated: 2026-10-23. Field names are proposed code identifiers, subject to implementation.

## Photo

| Field | Description |
|---|---|
| `id` | Unique ID within the library |
| `path` | Primary file path, preferring NEF |
| `pairPath` | Optional matching JPEG path for a NEF+JPEG pair |
| `captureTime`, `camera`, `lens`, `exif` | Read-only capture metadata |
| `rating` | 0–5 |
| `colorLabel` | `none \| red \| yellow \| green \| blue \| purple` |
| `rejected` | Rejection flag (L2) |
| `isFilmScan` | Whether this is a film scan (L2) |
| `rollId` | Associated roll for film scans |
| `params.l1` | L1 parameters mapped to `.nksc` |
| `params.l2` | L2 parameters mapped to `.nice` |

When Stack NEF + JPEG is enabled, a pair appears as one filmstrip item labeled `NEF+JPG`, with a stacked appearance. The information panel can switch between NEF and JPEG.

## Album

An album groups photos. A photo can belong to multiple albums; adding it never moves or copies the original file.

| Field | Description |
|---|---|
| `id`, `name` | — |
| `kind` | `normal \| smart \| temp` |
| `parentId` | Album nesting; selecting a parent includes photos in all descendants |
| `members` | Ordered photo IDs for `normal` and `temp` albums |
| `rule` | Smart-album rules, such as `rating >= 4`, membership, camera, date, or edited state |
| `writeKeywords` | Whether to write photo keywords; defaults to `true` for `normal`, `false` for `temp` / `smart` |
| `book` | Optional photo book data, described below |

Normal albums use their own order. Parent and smart albums sort by capture time.

## Roll

| Field | Description |
|---|---|
| `id`, `name` | — |
| `filmType` | `c41 \| bw \| e6` |
| `baseSample` | Sampled film-base color |
| `negParams` | Density, cyan–red, magenta–green, yellow–blue, dust removal, border crop |
| `profile` | Film profile |

## Book

| Field | Description |
|---|---|
| `size` | `sq20 (20×20) \| p21 (21×28) \| l28 (28×21) \| sq30 (30×30)`, in cm |
| `bleedMm` | Default: 3 |
| `safeMm` | Default: 5 |
| `binding` | Binding method and spine width |
| `vendorPreset` | Print-provider preset |
| `spreads` | Ordered spreads; first is front cover, last is back cover |

**Spread:** `template`, `placed` (photo frame → photo ID), and `text` (captions).

**Template:** photo/text frame geometry uses millimeters and relative positions. Edge-to-edge frames automatically extend to bleed. Resizing behavior: proportional scaling / reflow while retaining millimeter dimensions / locked dimensions. Embed a template copy in each project so library changes do not alter existing books.

Page sequence: front cover → 2–3 → 4–5 … → back cover.

## IdPhotoSpec

| Field | Description |
|---|---|
| `id`, `name` | For example, Schengen / UK / Japan |
| `widthMm`, `heightMm` | Photo dimensions |
| `headTopPct`, `chinPct`, `eyeBandPct` | Guide positions as percentages of photo height |
| `background` | Allowed background colors |
| `digital` | Digital pixel dimensions and file-size requirements (P1) |
| `source`, `checkedAt` | Required official source link and verification date |

Prototype guide values are illustrative. **Verify every production specification against official requirements** (Q-008).

## PrintJob

Paper, orientation, layout (`1 \| 2 \| 4 \| 9 \| id`), fit mode, margins, printed information fields, printer (including PDF), ICC, rendering intent, soft proofing, and copies. Print content is the photos selected in the filmstrip.

## Preferences

Theme (`dark \| gray`), view mode (`std \| focus \| immersive`), window / fullscreen, last source, monitor ICC, default directory, and shortcuts. Restore the last view mode and window state on launch.
