# Design prototypes

[English](README.md) | [简体中文](../../docs/zh-CN/prototype.md)

Last updated: 2026-10-23. Status: pending finalization.

The existing prototype interfaces are in Simplified Chinese. They are retained as design references under D-026; the [English specifications](../../docs/product.md) and [bilingual glossary](../../docs/glossary.md) explain the intended behavior and terminology. This documentation update does not establish a product localization policy.

## Online version

- [NICE interface concept canvas](https://claude.ai/artifact/KBSdQUMurHk8Eoqh34f8Gj), containing four designs.
- The canvas is private by default. The project owner must enable sharing before others can access it. Making this GitHub repository public does not change canvas access.

## Local snapshots

| File | Page |
|---|---|
| [Main.dc.html](Main.dc.html) | 01 Browse · sources / album management / photo information |
| [Edit.dc.html](Edit.dc.html) | 02 Edit · styles and film / adjustments ①–⑤ / negative conversion |
| [Album.dc.html](Album.dc.html) | 03 Album · page thumbnails / layout / available photos |
| [Print.dc.html](Print.dc.html) | 04 Print · layouts including ID photos / output |
| [canvas.json](canvas.json) | Canvas layout index |

These `.dc.html` files depend on the design canvas runtime and **do not run by opening them directly in a browser**. Use them to inspect structure, copy, interaction states, and token usage.

## Interpretation

- These are demonstration prototypes: photos are landscape illustrations, color adjustments use CSS filters, and data is hardcoded. **They do not prescribe implementation.** Follow the specifications in `docs/`.
- Each page stores its own state; there is no cross-page synchronization. Adding a photo to an album on Browse will not update other pages.
- Bracketed Chinese text such as `[打印机名称]` (printer name), `[印厂名称]` (print provider), and `[第二块硬盘]` (second drive) is placeholder content.
- ID photo guide positions are illustrative; production specifications require verified sources under [Q-008](../../docs/open-questions.md) and the [data model](../../docs/data-model.md).
- If a prototype differs from the documentation, follow [decisions](../../docs/decisions.md) and the specifications, and record the discrepancy in [open questions](../../docs/open-questions.md).
