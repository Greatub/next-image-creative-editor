# Decision log

[English](decisions.md) | [简体中文](zh-CN/decisions.md)

Last updated: 2026-10-24.

This is the highest-priority source of truth. Replacing a decision requires the project owner's confirmation and a new record explaining the replacement.

Status: **Confirmed** = approved by the project owner; **Proposed** = awaiting confirmation; **Superseded** = replaced by a later decision.

| ID | Date | Decision | Status |
|---|---|---|---|
| D-001 | 2026-10-23 | Product name: **NICE**, short for Next Image Creative Editor | Confirmed |
| D-002 | 2026-10-23 | Store editing parameters separately; original files are read-only | Confirmed |
| D-003 | 2026-10-23 | Parameter layers: L1 (`.nksc`, readable by NX Studio / Capture NX-D), L2 (NICE only), L3 (baked export) | Confirmed |
| D-004 | 2026-10-23 | Target Nikon users with deep support for Nikon formats and features | Confirmed |
| D-005 | 2026-10-23 | Provide Dark and Neutral Gray themes | Confirmed |
| D-006 | 2026-10-23 | Equal, fixed 300px side panels; all four pages share a stable shell without layout shifts | Confirmed |
| D-007 | 2026-10-23 | Three view modes: Standard (all fixed), Focus (only footer fixed), Immersive (all auto-hide), independent of window / fullscreen; no per-panel pins | Confirmed |
| D-008 | 2026-10-23 | Floating view-mode controls in a photo-stage corner appear with mouse movement and hide when idle | Confirmed |
| D-009 | 2026-10-23 | Use the shared footer for photo browsing and selection on all four pages | Confirmed |
| D-010 | 2026-10-23 | Optionally stack matching NEF and JPEG files as one photo item | Confirmed |
| D-011 | 2026-10-23 | Optional photo grids on Edit | Confirmed |
| D-012 | 2026-10-23 | Number Edit adjustments by workflow: ① Geometry ② Exposure and white balance ③ Tone ④ Color ⑤ Detail, matching processing order | Confirmed |
| D-013 | 2026-10-23 | Cover commonly used international ID photo specifications | Confirmed |
| D-014 | 2026-10-23 | Photo books export standard files (PDF / page images), without online-provider integration; show bleed guides | Confirmed |
| D-015 | 2026-10-23 | Provide built-in and custom photo book templates | Confirmed |
| D-016 | 2026-10-23 | Unify albums and tags as Albums: multiple membership, nesting, smart and temporary albums, and per-album choice of writing photo keywords | Confirmed |
| D-017 | 2026-10-23 | Put photo books on Album: page thumbnails on the left, available photos from the selected album in the footer | Confirmed |
| D-018 | 2026-10-23 | Manage albums in Browse's left panel; choose sources only on Browse (option B, avoiding footer / sidebar focus mismatch) | Confirmed |
| D-019 | 2026-10-23 | The footer source indicator is display-only to preserve layout stability | Confirmed |
| D-020 | 2026-10-23 | Exclude slideshows from this version; if restored, use a Presentation view on Album without a new navigation page | Confirmed |
| D-021 | 2026-10-23 | One location per feature (UI spec §8); theme in Settings; Save as PDF as a printer option; selection count only in the footer's right section | Confirmed |
| D-022 | 2026-10-23 | P0 includes import, editable ratings / color labels / rejection, sorting, zoom, white-balance presets / eyedropper / Picture Control, clipping warnings, soft proofing, ID portrait adjustment, book size / caption editing / page deletion, and export dialog | Confirmed |
| D-023 | 2026-10-23 | Film digitization belongs on Edit: Film profiles in the left Film tab; conversion settings in the right Negative conversion tab | Confirmed |
| D-024 | 2026-10-23 | Navigation order: Browse → Edit → Album → Print | Confirmed |
| D-025 | 2026-10-23 | Use Simplified Chinese for documents and English for code / identifiers; this project folder defines the specifications | Superseded → D-026 |
| D-026 | 2026-10-23 | Per the project owner's request, repository material is English first and Simplified Chinese second. Default documents use English; complete Chinese translations live in `docs/zh-CN/`, with reciprocal links. English is the maintenance baseline; keep translations synchronized. Retain existing Chinese design prototypes as references. This does not decide product interface language or change behavior. Replaces D-025's documentation language rule; other collaboration principles remain in force. | Confirmed |
| D-027 | 2026-10-24 | Per the project owner's request, the app icon is **v1.8.1** (`SVG Icon/nice_v1_8_1.svg`), refined from v1.8.0: matching clearance between the C and both bottom body lines, a wider C-to-E gap, and a wider E-to-body gap. `App Icon/` was re-exported from it (SVG, ICNS with the macOS 824/1024 grid, ICO, PNG 16–1024). Brand gray changes from `#7E858C` to `#6B7280` to match the icon; ink `#1E2732` and paper `#FAF9F6` are unchanged. Replaces the previous v1.7.1D-based icon set. | Confirmed |
