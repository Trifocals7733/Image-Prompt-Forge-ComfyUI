# MOSS-Image dialect

MOSS-Image takes a caption, or a caption plus a spatial layout of bounding boxes. The agent makes the creative decisions; the bundled Python tool does the coordinate arithmetic. Never submit anything but the caption or the compiled line as the positive prompt.

## Which deliverable

| Use | When |
|---|---|
| **caption** | ordinary scenes, photographs, illustrations, anything without critical placement |
| **Layout** | exact text placement, posters, several panels, controlled object relationships |

A detailed image does not automatically need Layout. Layout earns its place when *where* is part of what was asked.

## Caption

```json
{"caption": "…"}
```

One cohesive prose description. Spatial relations in words, any visible wording quoted exactly. Paragraphs allowed. No coordinates, no layout fields, no JSON jargon inside the text.

## Layout

Read `references/layout.md` first, then:

1. Draft readable JSON with `width`, `height` (multiples of 16), `bbox_coord_space: "abs"`, `bbox_format: "xyxy"`, `summary` (`caption`, optional `meta`, `canvas`), and a flat `items` array.
2. Each item: `label` (`object` | `character` | `text`), nonempty `caption`, four-number `bbox` in **actual pixels from the top-left**. `text` items also carry `text` — the exact characters, with intended line breaks as JSON `\n`.
3. Save the draft in the working directory, then compile:

```bash
python3 <skill>/scripts/compile_prompt.py draft.json --output prompt.txt
```

Use the real path to this skill's script. The compiler floors left/top and ceils right/bottom, subtracts the canvas centre, and emits flat `abs_centralized` / `yxyx` boxes in one physical line. It rejects invalid or out-of-bounds boxes instead of clipping them.

4. Read the compiled output and check subject, exact strings, geometry and canvas before delivering. Fix the draft, not the compiled line.

## Drafting rules

- **Place in pixels.** Never normalized coordinates, never manual centring or reordering, never percentage boxes.
- **Honour given dimensions.** Only an aspect ratio → choose compatible pixels. Nothing given → 1024×1024 unless the medium suggests another shape. Explicit incompatible dimensions are surfaced, never silently changed.
- **Boxes are placements, not drawings.** A box does not ask for a border, panel or sticker.
- **Group coherent units**, split only when independent placement matters. Overlap and occlusion are allowed.
- **Keep exact strings in `text` fields**; describe typography and surface treatment in the item's caption.
- **Check the fit.** A short Chinese title and a paragraph of Latin copy need very different footprints. Enlarge or recompose an unreadable layout rather than dropping copy or stretching glyphs.
- **Designed images get composed**, not decorated: reading order, relative prominence, coherent groups, copy written out rather than "headline here". Keep a minimalist brief minimalist; do not force every brief into title/hero/footer.

## Delivery

Caption → the JSON, or the caption text. Layout → the compiled single line plus the required `width×height` stated **outside** the prompt (the prompt itself carries no canvas metadata), and a link to the retained draft when it helps.

## Difference from the other two

No negative prompt, no sampler-based constraint trick, no instruction-edit mode. Anything that must be *changed* in an existing picture belongs to Qwen Edit or a Krea inpaint, not here.
