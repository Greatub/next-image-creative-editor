# Architecture

[English](architecture.md) | [简体中文](zh-CN/architecture.md)

Last updated: 2026-10-23. Status: concept design; technology stack undecided (Q-001 in [open questions](open-questions.md)).

## 1. Principles

- Original photo files (NEF / NRW / JPEG / HEIF) are **read-only**.
- Save edits as parameters and evaluate them during rendering. Create new pixel files only on export.
- Every parameter belongs to a declared layer (L1 / L2 / L3), identified in the interface.

## 2. Parameter layers

| Layer | Meaning | Storage | In NX Studio / Capture NX-D | Interface label |
|---|---|---|---|---|
| **L1 · Compatibility** | Adjustments supported by Nikon software: white balance, exposure compensation, Picture Control, and tone/color parameters with a direct mapping | `<photo-directory>/NKSC_PARAM/<filename>.nksc` | Intended to match; requires Q-002 validation | Restorable in NX Studio |
| **L2 · Extensions** | NICE-only effects and data: style presets, film profiles, grain, negative conversion, grid preferences, albums, layouts, ID photo parameters, photo status, etc. | NICE sidecar (proposed: `<photo-directory>/NICE_PARAM/<filename>.nice`, JSON) + NICE database | Unavailable, or only approximated | NICE only |
| **L3 · Baked output** | All L1 + L2 effects rendered into a new file during export | New file in the export directory | Matches the rendered output, as an ordinary image | — |

Rules:

- Use L1 whenever it can express the adjustment. Where possible, provide an L1 approximation for L2 effects, such as reversed per-channel curves for negative inversion. **Feasibility requires validation (Q-003).**
- Until validated, **never write data NX Studio does not recognize into `.nksc`**. Keep L2 separate.
- When an external application changes `.nksc`, refresh L1 from that file and retain L2 unchanged.

## 3. Photo metadata

| Data | Storage | Notes |
|---|---|---|
| Ratings and color labels | Undecided (Q-005) | Validate that NX Studio reads ratings written by NICE |
| Rejection flag | L2 | — |
| Album membership | Hierarchical XMP/IPTC keywords, such as `Travel\|Japan\|Kyoto`, when Write photo keywords is enabled; otherwise only the NICE database | Enabled by default for normal albums; disabled for temporary and smart albums |
| Album hierarchy, order, and smart rules | NICE database (L2) | A photo may have different positions in different albums |
| Photo book layout | Book project data (L2), with embedded template copies | Photos are referenced by path; provide relinking after files move |

## 4. Processing pipeline

The order is fixed and matches the Edit panel numbering.

1. **① Geometry:** lens correction (vignetting, distortion, lateral chromatic aberration) → straighten → crop.
2. **Negative conversion**, only for film scans: film-base correction → inversion → per-channel levels → density / color balance → dust removal.
3. **② Exposure and white balance:** white balance (presets / eyedropper / temperature and tint) → exposure → Picture Control.
4. **③ Tone:** contrast, highlights, shadows, whites, blacks.
5. **④ Color:** vibrance and saturation.
6. **⑤ Detail:** clarity, sharpening, noise reduction.
7. **Style / film profile:** blend by strength; grain.
8. **Output:** display (monitor ICC) / soft proofing (printer ICC) / export (target color space + output sharpening).

The design prototypes simulate these effects with CSS filters. This is for demonstration and **does not prescribe the implementation**.

## 5. Negative conversion (film digitization)

- Organize film scans by roll. Share film-base samples and conversion parameters within a roll, with Sync to roll.
- Support color negative (C-41), black-and-white negative, and color reversal film (E-6; apply a profile without inversion).
- Sequence: mark as film scan → sample film base from the leader or gaps between frames, shared across the roll → invert → auto levels with per-channel black/white points → density / cyan–red / magenta–green / yellow–blue → dust removal → automatically crop film borders and sprocket holes.
- Digital camera scans have no infrared channel. Dust removal relies on algorithms and has limited effectiveness.
- Film profiles in Edit's left Film tab apply both to restored film colors and to film looks for digital photos.
- All of this belongs to L2.

## 6. Import and output

- **Import:** copy from a memory card to a destination, optionally organizing by date, renaming, and backing up to a second drive. Allow NEF-only import and adding imported photos to an album. Never modify originals on the card.
- **Export (L3):** JPEG / 16-bit TIFF / HEIF; dimensions; sRGB / Adobe RGB; sharpening (none / screen / print); metadata (all / copyright only / remove location); filename rules.
- **Export print files:** print-ready PDF (PDF/X-4, bleed, optional crop marks, embedded ICC or CMYK conversion) / per-page JPEG / per-page TIFF. Print-provider presets store bleed, safe area, spine, and related parameters.
- **Print:** use the system print dialog. Soft proof with the selected printer + paper ICC and rendering intent.

## 7. Technical validation required before development

| ID | Validation |
|---|---|
| Q-002 | Reliable third-party `.nksc` read/write and acceptance by NX Studio |
| Q-003 | Whether unknown `.nksc` data is rejected or removed; feasibility of L2-to-L1 approximations |
| Q-004 | NEF decoding, including High Efficiency RAW, and licensing: Nikon SDK or other libraries |
| Q-005 | Rating / color-label storage and NX Studio interoperability |
| Q-006 | NX Studio reading of hierarchical XMP/IPTC keywords written by NICE |
| Q-007 | Custom Picture Control NP3 export to bring NICE looks back to the camera |

See [open questions](open-questions.md) for details and status.
