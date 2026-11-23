---
name: image-prompt-forge-comfyui
description: Use when writing, expanding, rewriting or debugging an image prompt for ComfyUI, Krea 2, MOSS-Image or Qwen-Image 2.1 — a one-line idea, a reference image or recurring character, a poster or layout with exact text, an image edit or inpaint, reverse-prompting a picture, or the sampler settings that go with it. Also use when the user describes a picture and expects a prompt back without naming a model.
---

# Image-Prompt-Forge-ComfyUI

Turn a half-formed wish into the prompt the chosen model actually responds to, plus the settings that go with it. Text and settings only: no renders, no ComfyUI calls, no image files written.

## Models this speaks

| Model | Default for | Native strength | Prose form |
|---|---|---|---|
| **Krea 2** (Turbo / RAW) | anything unnamed | photographic direction, style control, reference images | 1–2 paragraphs |
| **MOSS-Image** | designed layouts, exact text placement | bounding-box layout, poster composition | caption, or Layout JSON |
| **Qwen-Image 2.1** | edits, dense text, busy frames | text rendering, instruction edits, multi-image | one long paragraph |

When the brief names no model, write **Krea 2 Turbo** and say so in the assumption line. Never silently swap models.

## Pipeline

1. **Read the brief** (below).
2. **Route**: pick model and mode.
3. **Build the scene graph** (below). Creative gaps are yours to fill.
4. **Load the dialect**: read the matching reference file. Do not write from memory of another model's rules.
5. **Compile** in that model's form and length.
6. **Self-check** against the list at the end.
7. **Emit** the output contract.

## Read the brief

Split everything the user said into three piles before writing a word:

- **Fixed** — must survive unchanged: visible text strings, named objects, counts, stated colours, stated placement, aspect ratio, what must not appear.
- **Silent directives** — instructions about the job, not the picture ("make it sharper", "keep it simple", "4K"). Obey them; never echo them into the prompt.
- **Open** — everything else. A five-word brief means you are inventing most of the frame, not writing less.

Rules for the open pile:

- **Fill it boldly.** Choose the shot, lens, light direction and quality, palette, materials, styling, wardrobe, mood, and the frame's edges. A thin brief deserves a full picture; that is the whole point of using this skill.
- **Never fabricate** brand names, real people's likeness, legible text the user did not ask for, or factual claims about a subject's identity, biography or location. Description of what is visible is fair game; assertion of fact is not.
- **Keep the impossible.** If the brief asks for something physically wrong, write it as the image shows it and keep the rest of the frame coherent.
- Ask a question only when the answer changes the picture (which model to use, what the visible words are, whose face). Otherwise decide and disclose in the assumption line.

## Scene graph

Before prose, settle these five lists. They are what prevents the classic failures — cloned subjects, garments swapped between people, props with no owner.

1. **Entities**: people, animals, objects, architecture, text. Exact count; primary vs secondary.
2. **Attributes**: appearance, wardrobe, materials, colours, scale — each bound to one entity by name.
3. **Actions**: posture, gesture, gaze, contact, movement direction, weight.
4. **Relations**: left/right, foreground/midground/background, in front of / behind / inside / holding / facing / overlapping.
5. **Layers**: what sits in each depth plane and what the frame cuts.

Two or more similar subjects get independent sentences. A group ("three women") is not an entity — name each one's position and action or drop to one subject.

## Route

| The brief | Model | Mode |
|---|---|---|
| a picture idea, no model named | Krea 2 Turbo | `create` |
| an existing prompt to sharpen | the prompt's own model | `enhance` |
| one or more images to change | Qwen 2.1 Edit | `edit` |
| a mask exists, or a frame must stay pixel-locked | Krea 2 inpaint | `edit` |
| transfer this garment onto this person | Krea identity-edit pack if installed, else Qwen Edit | `edit` |
| exact text placement, poster, multi-panel | MOSS-Image | `layout` |
| a picture to describe back into a prompt | Krea 2 | `reverse` |
| controlled A/B or exploration grid | the brief's model | `variants` |

Explicit model in the brief always wins. Then the mode, then this table.

## The dialect differences

These are the differences worth keeping in your head; each model's file holds the detail.

| | Krea 2 | MOSS-Image | Qwen-Image 2.1 |
|---|---|---|---|
| Form | 1–2 paragraphs, 80–350 words | `{"caption":"…"}` or Layout JSON + compiled line | T2I: one paragraph, ~20 sentences / 400–500 words. Edit: one instruction paragraph |
| Language | English | English | English prose, always; rendered text in its own script |
| Position | grammatical spatial relations | Layout: absolute pixel boxes | 8–14 positional phrases, a third of sentences open on position |
| Text in image | unreliable past ~5 words; count it | reliable; boxes pin placement and size | strongest; quote every legible string |
| Negatives | dead at CFG 1.0 — write positive constraints | no negative field | no negative field; state affirmatively |
| Ratio | keep out of prose; settings line | canvas w×h, multiples of 16 | `wh_ratio` or `ratio_follow`, never in prose |
| Reference images | `Picture N` clause, 3 max, layout split at 0.25 | describe in the caption | `<image1>`, `<image2>` mandatory for N≥2 |
| Edit behaviour | mask frees pixels, prompt describes the whole finished frame | not an editor | instruction-led, edit only what is named, hold the rest |
| Default settings | 8 steps, CFG 1.0, euler, shift 4.0 + beta | model card; dims %16 | Lightning 8-step CFG 1.0, or 50 steps CFG 4.0 |

Text-heavy briefs: honor the model that was named and warn in one line that Krea loses lettering past about five words. Do not reroute silently.

## Modes

### create (default)

Scene graph → dialect → compile. Krea: subject first, camera and light concrete, quality adjectives cut. MOSS: caption, or Layout when placement matters. Qwen: the eight-step long-paragraph walk.

### enhance

Keep the user's content, fix the form. Preserve every fixed element verbatim; rewrite toward the target dialect's length, order and constraint language. Report what changed in one line, not a diff.

### edit

- **Qwen Edit** is the default. Lead with the operation, name only the attribute being changed, push it to full strength, and hold everything else with one blanket preservation clause. Describe appearance only for what changes. Tag inputs `<image1>…` when N ≥ 2 and state each image's role. Pick the canvas: the image whose composition survives supplies `ratio_follow`.
- **Krea inpaint** when a mask exists. The prompt describes the whole finished picture; the mask only decides how much may move. Never mention a removed object — at CFG 1.0 the mention wins. Read `references/krea2.md`, "Masked edits".
- **Garment swap** with a face to keep: prefer the local identity-edit pack when installed; otherwise Qwen Edit with the person as canvas and the outfit as the material source.

### layout (MOSS)

Use only when placement or exact type matters. Read `references/moss.md`; draft JSON, run the bundled compiler, deliver the compiled line plus the canvas size. Boxes are actual pixels from the top-left, absolute `xyxy`.

### reverse

Describe only what is visible. Separate observed from likely; mark the genuinely uncertain as "likely" or leave it out. Rebuild in visual priority order: medium → subject → pose → appearance → camera → environment → light → palette → materials → style → text. Then state the settings the reference implies. Never invent detail to make the prompt read better.

### variants

One variable per variant, everything else fixed. State the variable above each prompt. Default to 3 variants unless asked otherwise.

### evaluate (on request)

Name the contradictions, unbound attributes, missing control information and the two most likely failure modes, each with the clause that causes it.

## Output contract

Unless the user asks otherwise, return exactly this:

~~~
```
<the prompt, copy-ready, nothing else inside the block>
```

Model: Krea 2 Turbo · 2048×1152 (16:9) · 8 steps, CFG 1.0, euler, shift 4.0 + simple
Assumed: night, wet asphalt, one figure — the brief left these open
~~~

- Line 2 carries only what the model in play needs: Qwen T2I adds `wh_ratio`, Qwen Edit adds `wh_ratio` + `ratio_follow`, MOSS Layout carries `width×height` outside the prompt. Offer the Qwen JSON payload only when the user asks for API/script form.
- Line 3 is omitted when the brief fixed everything.
- No commentary inside the prompt block. No bilingual duplicates. No "here is your prompt".
- Variants, scores, scene graphs and negative prompts only on request.
- Rendered text stays character-exact in its own script. Never translate, correct or re-capitalise it.

## Self-check before emitting

- Do the counts match the brief, and is every named entity present exactly once unless asked otherwise?
- Is every attribute bound to its owner — no garment, colour or prop floating between two subjects?
- One medium, one shot size, one camera angle, one light direction. Any contradiction is a bug.
- Does the composition fit the chosen ratio, and does the ratio appear only where that model wants it?
- Is every legible string quoted and placed, and is there any unrequested lettering implied?
- Any quality stack left (`masterpiece`, `8K`, `ultra detailed`, `best quality`)? Cut it and spend the words on camera, light, material.
- Any negation that a CFG 1.0 model cannot hear (`no people`, `no text`)? Rewrite as the visible positive state.
- Any invention beyond art direction — brand, real likeness, biographical claim, text? Remove it.
- Is the prompt in the right length band for its model, and in English throughout?
- Would a renderer have to guess at anything the brief and the scene graph already settled?

## Adult and sensitive work (18+)

When the brief is explicitly adult, read `references/adult.md` before writing and keep the register clause first. Otherwise do not load it and do not add sexual content to a brief that did not ask for it.

## References

Read only the file the route needs. Paths are relative to this skill.

| File | Read when |
|---|---|
| `references/krea2.md` | any Krea prompt: prose shape, positive constraints, tested failure table, references, inpaint |
| `references/qwen-2-1.md` | Qwen T2I or Edit: the eight-step paragraph, edit protocol, ratio payload |
| `references/moss.md` | MOSS caption or Layout, plus the compiler |
| `references/vocabulary.md` | camera, lens, film, light, material, colour, and the style formulas |
| `references/settings.md` | sampler, LoRA, resolution for the model in play |
| `references/layout.md` | writing a MOSS Layout draft |
| `references/poses.md` | a pose that must be exact; hands and held objects |
| `references/pose-library.md` | 30 ready pose blocks to adapt |
| `references/adult.md` | explicit 18+ briefs only |
| `scripts/compile_prompt.py` | compile a MOSS Layout draft (never hand-write the compact form) |
