# Open questions and validation

[English](open-questions.md) | [简体中文](zh-CN/open-questions.md)

Last updated: 2026-10-23.

Record the ID, question, why it matters, and current status / recommendation. When resolved, record the conclusion in [decisions.md](decisions.md) and mark this entry “Resolved → D-xxx”.

## Technology

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-001 | Desktop stack: native Swift + C++/Windows, Qt, Electron/Tauri + native imaging core, etc. | Determines performance, color management, printing, and RAW integration | Undecided. The image pipeline needs GPU acceleration and 16-bit depth; evaluate a native imaging core with a cross-platform UI |
| Q-004 | NEF decoding, including High Efficiency RAW, and licensing | The product depends on decoding support | Undecided. Investigate Nikon SDK access / license terms, or third-party library support for HE/HE* |

## Nikon interoperability: validate before development

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-002 | Can third parties reliably read/write `.nksc`, and will NX Studio accept it? | Core value proposition | Unverified. The format is not public; build a read/write prototype and reopen it in NX Studio |
| Q-003 | What happens with unknown `.nksc` data? Can L2 effects be approximated in L1, such as negative inversion with reversed curves? | Determines L2 storage and fallback strategy | Unverified. Keep L2 separate until validated |
| Q-005 | Where to store ratings / color labels, and how to interoperate with NX Studio? | Culling results must agree across both applications | Unverified |
| Q-006 | Does NX Studio read hierarchical XMP/IPTC keywords written by NICE? | Determines the value of album-to-keyword writing | Unverified |
| Q-007 | Is custom Picture Control NP3 export feasible? | Could bring NICE looks back to cameras | Unverified; not required for v1 |

## Product and content

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-008 | Sources and maintenance for ID photo specifications: official requirements, check dates, update process | Wrong specifications can cause rejection | Build a specification table with source links and verification dates; do not claim guaranteed compliance |
| Q-009 | Should film profiles use real film stock names? | Trademark considerations | Prototypes use descriptive names such as Portrait Negative 160; real stock names require legal confirmation |
| Q-010 | Font embedding licenses for photo book PDFs | Output licensing | Use freely embeddable open-source fonts such as Source Han Sans / Serif in built-in templates; check custom-template embedding rights before output |
| Q-011 | Should the yellow accent `#F2C230` align with icon colors `#1E2732`, `#7E858C`, and `#FAF9F6`? | Brand consistency | Pending brand confirmation. Keep yellow for now; icon colors are in the `brand` token group |
| Q-012 | Should photo status (Unprocessed / Editing / Complete) and editing workflow templates enter v1? | Scope | Discussed but not confirmed; pending |
| Q-013 | What exactly does the Settings compatibility-layer switch mean, such as L1-only writing or disabling L2 effects? | A safe mode for NX Studio-only users | Design pending |
| Q-014 | How does simultaneous multi-photo editing relate to Copy to selected? | Avoid duplicate entry points | Design pending; prefer retaining only Copy to selected |

## Documentation language

| ID | Question | Why it matters | Status / recommendation |
|---|---|---|---|
| Q-015 | The English-first repository request conflicts with D-025's Chinese documentation rule | Establish the public repository's primary language while preserving decision history | Resolved → D-026; the project owner explicitly requested English first, Chinese second on 2026-10-23 |
