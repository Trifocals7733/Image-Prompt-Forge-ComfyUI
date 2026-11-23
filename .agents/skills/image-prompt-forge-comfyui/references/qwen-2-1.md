# Qwen-Image 2.1 dialect

Two modes, two shapes. **T2I** is one long observer paragraph. **Edit** is one instruction paragraph plus a payload describing output size. Both prose bodies are English — always, whatever language the request arrives in. Only text *inside the image* keeps its own script, verbatim.

## T2I: the eight steps

Work in order; later steps never revise an earlier decision.

1. **Split the brief.** Fixed (text strings character-for-character, named objects, counts, colours, positions, ratio) · silent directives (job instructions, obeyed and never echoed) · open (everything you decide).
2. **Fix the frame.** User ratio wins. Otherwise `3:2` horizontal, `2:3` vertical, `1:1` badge/icon/cover, `16:9` wide presentation, `9:16` or `1:2` phone and banner. The ratio lives only in `wh_ratio` — never write a ratio, resolution or pixel count into the prose.
3. **Opening sentence, ~20 words.** Medium noun is mandatory: `The image is a vertical editorial photograph of …`; name style, subject, background and palette. `This is a…` or `A vertical realistic photograph of…` are equivalent.
4. **Inventory before prose.** A spatial list — every element with a place (upper-left, across the top, lower third, behind the left shoulder) — needing **8–14 positional phrases**, reaching corners, edges and centre, not clustering. Plus a text list in reading order.
5. **Walk the frame.** Regions (poster, page, interface, wide scene): background surface first, then top band, then left → centre → right, then bottom band. Single subject: background falloff, pose and placement, head and face, body and garments, held objects, edges. About a third of sentences should open on the positional phrase. One paragraph; break only for genuinely stacked regions, one paragraph per region.
6. **Set every string.** Skip when nothing is legible — inventing signage is a mistake. Otherwise, in reading order: `a bold black headline across the top reads "夏日特惠"`. Quote in the original script, give weight, colour, case, relative size. Describe a line break as a second line, never a real newline inside the string. Marks that are not meant to be read are called blurred or too small. Chart axes, ticks, legends and cell values are text too.
7. **Lighting gets its own sentence** — source, direction, quality, shadows and highlights. Flat even ambient for diagrams.
8. **Close with one whole-frame sentence**: `The overall composition is …` covering balance, palette, style, mood. Exactly one; no second summary.

**Register.**

- ~20 sentences, ~450 words, ~25 words per sentence — the same size for a three-word brief as for a dense one. A thin brief means you invent more, not write less.
- Present tense, third person, declarative. No "you", no "create", no "make sure", no quality boosters.
- Hedge what cannot be known: `appears to be`, `likely`, `a notebook or a tablet`. Be definite only about what the user fixed.
- Colour names take a modifier: deep navy, muted olive, pale cream, warm terracotta. Hex only if given.
- Give material, not just the noun: brushed metal, matte plastic, coarse linen, condensation, paper fibre.
- Enumerate, never summarise. `Several items` is not a description.
- People get observable surface: build, posture, gaze, expression, hair, skin tone, each garment with colour and material. Age as a life stage, never a number of years.
- Objects by class, not brand, unless named. Physical coherence: shadows fall away from the light, reflections match, scale is consistent.

## Edit: the protocol

An input image is always present. Lead with the operation, written for someone holding only the images.

**Attribute disentanglement.** Change exactly what was named, at full strength; hold everything else at input fidelity. The two failures are symmetric — leakage (sharpening that re-grades colour, an upscale that reframes, a cleanup nobody asked for) and under-editing (output indistinguishable from input). Preservation locks content, never edit strength.

- **Anchor on the image.** Every spatial, tonal and contextual claim comes from what is visible. If unsure a detail exists, leave it out.
- **Say what stays without repainting it.** Name untargeted content by type, position, role; prefer one blanket preservation clause. The more concretely you describe what you mean to keep, the more likely it drifts.
- **Identity is the hardest invariant**: a person's face and recognisable accessories, a product's exact design and count, the input's medium. When identity comes from a reference image, point at the image rather than describing features in words.
- **Commit to a reading** when the instruction is vague, contradictory or offers alternatives. Keep the user's verb, spatial relations and stated state; keep physically impossible intent.
- **Only what was asked.** Do not clean up unmentioned defects or clutter. When an edit removes, moves or reveals something, describe the newly exposed region enough to stay coherent.
- **Text is literal.** Quote every readable string exactly, nothing summarised. Text you cannot commit to should not be added. Canvas extension is named as outpainting.
- **Scene generation from a reference** (placing the subject somewhere new, building a poster or shoot from it) is constructed actively: design scene, light, composition and layout to a professional standard, scaled to what was asked.

**Image references.** N ≥ 2 → mandatory `<image1>`, `<image2>` tags, never "the first image". N = 1 → no tags, refer to "the image". State each image's role: which is the canvas whose composition survives, which supplies material, and what is taken from each. A scene with no canvas (group shot) treats every image as an identity source, one described sentence each.

**Output size.** `wh_ratio` and `ratio_follow` are mutually exclusive; one is always `""`.

| Situation | `wh_ratio` | `ratio_follow` |
|---|---|---|
| User gave a ratio, dimensions or a keyword (`square`/`avatar`/`album` → 1:1, `landscape`/`wallpaper`/`PPT` → 16:9, `poster` → 2:3, `portrait`/`Stories` → 9:16, `cinematic` → 21:9, `iPad` → 4:3, `A4` → 5:7 or 7:5) | that ratio | `""` |
| Single-image edit, no size given | `""` | `"<image1>"` |
| Single image used as identity only, new scene | 2:3 portrait / 3:4 full-body / 3:2 landscape | `""` |
| Composite, face swap, clothing swap, style transfer, background replacement | `""` | the canvas image |
| Scene with no canvas | 3:2 group, 2:3 portrait, 16:9 wallpaper | `""` |
| Outpaint | infer from the extension direction | `""` |
| Panorama | 2:1, or 3:1 wide | `""` |
| Three-view of a person / 2×2 grid | 1:1 | `""` |

`2K`, `4K`, `8K` are quality words, never ratio hints.

## Output form

Interactive: the prompt in one block, plus `wh_ratio` (and `ratio_follow` for edits) in the settings line.

API or script form, only when asked — one line, no fences:

```json
{"rewritten_prompt": "…", "wh_ratio": "3:2"}
{"rewritten_prompt": "…", "wh_ratio": "", "ratio_follow": "<image1>"}
```

Edit prose is one continuous paragraph with no newlines. Never include resolution or ratio keywords inside the prompt string.
