# Krea 2 dialect

Krea 2's text encoder is **Qwen3-VL, a language model**. It parses full sentences, reads order as emphasis, accepts a reference image as conditioning, and was trained against the plastic "AI look". Four consequences: prose beats tag soup; what comes first is the subject; realism comes from a photographic setup, not from adjectives; an attached reference outranks anything you write about it.

Two variants, never mix their numbers: **Turbo** (8 steps, CFG 1.0, distilled — what you run) and **RAW** (base checkpoint, ~52 steps, CFG 3.5, for fine-tuning and a live negative branch). Hosted Medium/Large is a different product with sliders, not samplers.

## Prompt shape

One or two coherent paragraphs, 80–350 words:

```
medium/quality restraint → primary subject → pose and action → appearance and wardrobe
→ camera and composition → environment and layers → light → colour → materials
→ style and mood → text → positive constraints
```

Sentence frame:

> A [medium] of [subject], [appearance and action], set in [environment]. The composition uses [shot, angle, lens], placing [subject] relative to [frame and other elements]. Light comes from [direction/source] with [shadow and palette]. [Materials and micro-detail] stay visible, creating a [mood] atmosphere. [Constraints].

Lead with the thing that matters. Name a concrete medium (`editorial fashion photography`, `documentary photograph`, `architectural photography`) where a weak prompt says `beautiful`.

## Every prompt

- **State what is present.** At CFG 1.0 negation is inert, in prose and in the negative field. `no people` → `an empty street`; `no plastic skin` → `skin with visible pores`. A negated noun can backfire into the thing you banned.
- **Cut the quality stack.** `8k, masterpiece, best quality, ultra detailed` buys nothing and makes the image generic. Spend the words on camera, lens, light, material.
- **Text: five words maximum, double-quoted, on a flat surface facing the camera** — `a neon sign reading "OPEN LATE"`. Curved labels, rolled scrolls and angled surfaces come back with invented strokes or mirroring.
- **Count anything that must appear once.** `exactly one small brass plate reads "OMP", and the neon sign is the only other lettering in the scene`. An uncounted label duplicates. Keep the prop list short; long lists clone figures.
- **Dress the character top to bottom.** Name the lower garment and hosiery in the same clause as the top. A clause that stops at the hoodie renders nothing below the waist. Name what hangs open and what sits close: an apron bib or buttoned coat flattens any figure.
- **Emphasis goes in words.** `oxblood` beats `(red:1.4)`. On the stock encoder a weight shoves the whole conditioning; past ~1.2 the image falls apart. Weighting needs the `Krea2PromptWeight` node.
- **Sort a defect before rewording.** Render one prompt on four seeds. Defect on 3–4 seeds → the prompt: delete or restage the element. On 1–2 → the seed: keep a clean one. Change one field per iteration on a held seed.

## Tested failures

| Brief contains | Do this |
|---|---|
| A line over five words, or a speech bubble | Render an empty bubble, typeset after. Ask for the bubble in the composition or the head fills the top of frame |
| A screen: chart, chat, terminal, dashboard | Generate the area empty, paste a real screenshot. Rewording never fixes an interface |
| A hand holding an object | Open palm presenting it, or a cuff/handle/stick for the fist to wrap. Name the contact and the finger count |
| A figure at a table, desk, counter, bench | Delete the furniture, move its props into her hands or onto the floor. Depth wording buys nothing |
| A detached or uncanny prop | Give it a material the character cannot share, and state a visible gap between prop and body |
| A recurring character | One clean single-pose reference; open with `Keep the same <who> from Picture 1: <identity block>` |
| Two characters from two references | One side and one distinct action each, plus the 0.25 layout split |
| Three or more figures | Render the location with one figure, each other figure alone, then composite. The model tiles one figure |
| Pale or matching skin tone | Soft overcast daylight at neutral white balance instead of flash; tie the tone to a named body part |
| A LoRA that seems dead | A/B at a fixed seed up to ±3–5 before writing it off |
| A change to an existing picture | Masked inpaint, never a re-roll |

## Text, bubbles, screens

Lettering that renders: `The only large readable word in the image is "FIREFLY".` A label with no count comes back twice; name the frame's other lettering too. A prop carrying a word is drawn large enough to letter it — plan the composition around it.

Bubble: `one large blank cream speech bubble with a crisp charcoal outline in the top quarter of the frame, its interior entirely empty`. Set type at 4–5% of the long edge, two short lines. The tail is the fragile part.

Screen: spell out emptiness as content (`a message area completely empty warm cream from side to side, a single flat plane with no bubbles, no text, no icons`), keep hands and silhouette out of the paste zone, multiply a light-theme screenshot over the paper colour, keep a dark one opaque.

## Negative prompt

Turbo at CFG 1.0: submit none, or keep one narrow clause for a single recurring artifact. RAW or any live negative branch may use families: general quality, people/anatomy, realistic integration (floating character, fake compositing, mismatched lighting), landscape depth, poster text, saturation. Drop `text` when text is wanted; keep `watermark, logo` unless asked.

## Reference images

The encoder carries vision tokens: identity, outfit, proportions and art style enter through the prompt, and the reference outranks adjectives. Say `Picture N` for every connected image or it is ignored. Identity first, scene second, style last. One reference per character; three maximum. The reference owns proportions, expression and small accessories — an accessory the prompt never names still leaks in as a stray mark.

Two references: split the schedule so the text-only conditioning owns steps 0.0–0.25 and the reference conditioning 0.25–1.0 (ComfyUI: `ConditioningSetTimestepRange` + `ConditioningCombine`). Keep the split at 0.25–0.35; at 0.5 identity weakens and framing drifts. Give each character one side and one distinct action. If a duplicate survives, drop a character so one reference stays wired.

A sheet that does not carry a trait will not render it: rebuild the sheet instead of stacking identity adjectives.

## Identity block

Two or three sentences of visible, checkable attributes: hair, eyes, face shape, skin tone, build, signature clothing, one countable marker. Place it right after the medium clause. Pin what drifts (tie height, parting, the mark's exact spot). Keep scene state — expression, flush, sweat — out of the block. Reuse verbatim; change only the scene clauses.

## Masked edits

Prompt is global, the mask only frees pixels. Write the finished picture; the black fill says how much may move. Removal requires **zero mentions** of the object — a leftover clause puts it back. Contradictions resolve toward the mention. A box touching the frame edge leaves a dark rounded border; keep a margin or blacken the whole frame. Moving anatomy needs a whole-frame regeneration, not a mask. Crop → re-render at higher density → paste through the mask is the route for faces, hands and detail.

## Resolution

| Use | Ratio | Typical |
|---|---|---|
| Portrait / headshot | 1:1 | 1024², 1536² |
| Cinematic hero | 16:9 | 1920×1080, 2048×1152 |
| Panoramic | 21:9 | 2560×1080 |
| Broad environment | 4:3 | 1600×1200, 2048×1536 |
| Vertical poster | 2:3 | 1536×2048 |
| Phone poster | 9:16 | 1080×1920 |

Dimensions divisible by 16. Native is 2048; under 1024 loses the training distribution. Settings: `references/settings.md`.
