# Open questions and validation

[English](open-questions.md) | [简体中文](zh-CN/open-questions.md)

Last updated: 2026-10-24.

Record the ID, question, why it matters, and current status / recommendation. When resolved, record the conclusion in [decisions.md](decisions.md) and mark this entry “Resolved → D-xxx”.

## Technology

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-001 | Desktop stack: native Swift + C++/Windows, Qt, Electron/Tauri + native imaging core, etc. | Determines performance, color management, printing, and RAW integration | Undecided. The image pipeline needs GPU acceleration and 16-bit depth; evaluate a native imaging core with a cross-platform UI |
| Q-004 | NEF decoding, including High Efficiency RAW, and licensing | The product depends on decoding support | Undecided. First data point (2026-10-23): LibRaw 0.22 decodes a NIKON Z 30 NEF (uncompressed-tag, 5600×3728, crop 5568×3712) correctly; half-size decode ≈0.3 s. High Efficiency RAW not yet tested (needs a sample from a supporting body). Still investigate Nikon SDK access / license terms |

## Nikon interoperability: validate before development

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-002 | Can third parties reliably read/write `.nksc`, and will NX Studio accept it? | Core value proposition | **Feasible on NX Studio 1.11 (macOS)**, 2026-10-23. The file is an XMP text packet; edits live in an XML-escaped filter list. NICE-written exposure and rating changes were accepted by NX Studio. White balance and Picture Control use binary sub-blocks, partially decoded. See [research/nksc-format.md](research/nksc-format.md). Still to test: other NX Studio versions, Windows, Capture NX-D |
| Q-003 | What happens with unknown `.nksc` data? Can L2 effects be approximated in L1, such as negative inversion with reversed curves? | Determines L2 storage and fallback strategy | Partly answered. A custom-namespace element inside `rdf:Description` **survived an NX Studio 1.11 re-save** (one test). Recommendation: keep L2 in a separate `.nice` file as designed (D-003) — one version's behavior is not a guarantee — and optionally write only a small NICE marker into `.nksc`. L1 approximation of L2 effects is still untested |
| Q-005 | Where to store ratings / color labels, and how to interoperate with NX Studio? | Culling results must agree across both applications | Rating answered: stored in the XMP packet embedded in `.nksc` (`xmp:Rating` + `MicrosoftPhoto:Rating`); a NICE-written 5-star rating showed correctly in NX Studio. `nine:Rating` is not used. Color labels (`nine:Label`) still untested |
| Q-006 | Does NX Studio read hierarchical XMP/IPTC keywords written by NICE? | Determines the value of album-to-keyword writing | Unverified |
| Q-007 | Is custom Picture Control NP3 export feasible? | Could bring NICE looks back to cameras | Unverified; not required for v1 |
| Q-016 | Can NICE render L1 parameters so the result matches NX Studio (lens correction for vignetting / distortion, Picture Control tone curves, Active D-Lighting, white balance)? “NX Studio can read the parameters” and “NICE renders them the same way” are separate claims | The “Restorable in NX Studio” label implies visual consistency between the two apps | Partly answered (2026-10-23), see [research/render-parity.md](research/render-parity.md). Against NX Studio 1.11 exports of `DSC_3183`: plain LibRaw ΔE2000 ≈ 14 mean; after modeling lens geometry, vignetting, tone curve and a color matrix ≈ 1.8 mean low-frequency (in-sample), vs ≈ 0.6 between the camera JPEG and NX Studio. Pixel-identical parity is not achievable this way; “close by eye” is plausible with calibration. Largest gaps: default tone (Picture Control curve / baseline exposure / Active D-Lighting), then distortion and vignetting. NX Studio exposure is not a linear gain. The NEF embeds a full-size Nikon-rendered preview. Recommendation: “Restorable in NX Studio” refers to parameters, not pixels; plan a calibrated Nikon-like default (option B); keep Nikon SDK rendering under Q-004. Owner decision needed: show the embedded preview before first edit, or always NICE’s render. Next: NX exports with ADL off, lens corrections off, other Picture Controls, a second scene |
| Q-017 | What are Nikon's allowed ranges for each Picture Control parameter, per profile? | NX Studio reads out-of-range values silently and clamps only on re-save; NICE must never write invalid values | Open. Observed: with **Auto**, contrast is limited to ±2 (a NICE-written −3 was clamped to −2 when NX Studio re-saved). Map every parameter and profile, one change at a time |

## Product and content

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-008 | Sources and maintenance for ID photo specifications: official requirements, check dates, update process | Wrong specifications can cause rejection | Build a specification table with source links and verification dates; do not claim guaranteed compliance |
| Q-009 | Should film profiles use real film stock names? | Trademark considerations | Prototypes use descriptive names such as Portrait Negative 160; real stock names require legal confirmation |
| Q-010 | Font embedding licenses for photo book PDFs | Output licensing | Use freely embeddable open-source fonts such as Source Han Sans / Serif in built-in templates; check custom-template embedding rights before output |
| Q-011 | Should the yellow accent `#F2C230` align with icon colors `#1E2732`, `#6B7280`, and `#FAF9F6`? | Brand consistency | Pending brand confirmation. Keep yellow for now; icon colors are in the `brand` token group |
| Q-012 | Should photo status (Unprocessed / Editing / Complete) and editing workflow templates enter v1? | Scope | Discussed but not confirmed; pending |
| Q-013 | What exactly does the Settings compatibility-layer switch mean, such as L1-only writing or disabling L2 effects? | A safe mode for NX Studio-only users | Design pending |
| Q-014 | How does simultaneous multi-photo editing relate to Copy to selected? | Avoid duplicate entry points | Design pending; prefer retaining only Copy to selected |

## Documentation language

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-015 | The English-first repository request conflicts with D-025's Chinese documentation rule | Establish the public repository's primary language while preserving decision history | Resolved → D-026; the project owner explicitly requested English first, Chinese second on 2026-10-23 |
