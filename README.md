# Image-Prompt-Forge-ComfyUI

[![skills.sh](https://skills.sh/b/Trifocals7733/Image-Prompt-Forge-ComfyUI)](https://skills.sh/Trifocals7733/Image-Prompt-Forge-ComfyUI)

A skill for writing image prompts, built around three models that want very different prompts: Krea 2, MOSS-Image and Qwen-Image 2.1.

Describe the picture you want in a line or two. The skill picks the model that suits the brief, writes the prompt in that model's own dialect, and returns the prompt with the sampler settings that go with it. Nothing is rendered: the output is text you paste into ComfyUI.

The reason it exists: each of these models fails differently. Krea loses lettering past about five words and treats negations as decoration. MOSS wants bounding boxes in real pixels before it will place anything. Qwen wants one long observer paragraph where Krea wants two tight ones. One prompt cannot serve all three.

## What it handles

- A vague idea, expanded into a full prompt with camera, light, palette and materials
- Recurring characters and reference images, with the `Picture N` clauses and layout split Krea needs
- Posters, UI mockups and anything with exact visible text, including MOSS Layout drafts with real coordinates
- Edits and inpaints, routed to Qwen Edit or a Krea masked edit depending on what has to survive
- Reverse prompting, turning a picture back into a prompt without inventing what is not visible
- Sampler, LoRA and resolution settings for the model in play

## Install

```bash
git clone https://github.com/Trifocals7733/Image-Prompt-Forge-ComfyUI.git
cp -r Image-Prompt-Forge-ComfyUI/.agents/skills/image-prompt-forge-comfyui ~/.agents/skills/
```

Project scope works the same way, for a skill that only applies to one repo:

```bash
cp -r Image-Prompt-Forge-ComfyUI/.agents/skills/image-prompt-forge-comfyui your-project/.agents/skills/
```

pi discovers both `~/.agents/skills/` and a project's `.agents/skills/`. Any other harness that reads a `SKILL.md` will load it too. The MOSS layout compiler needs Python 3; nothing else does.

## Use

Ask for a picture. Name a model if you want one, otherwise it writes Krea 2 and says so.

```
woman in a rainy alley, moody
```

You get back the prompt, one settings line, and one line naming whatever it decided for you:

```
<the prompt, ready to paste>

Model: Krea 2 Turbo · 2048×1152 (16:9) · 8 steps, CFG 1.0, euler, shift 4.0 + beta
Assumed: night, wet asphalt, one figure
```

Variants, scoring and scene breakdowns are available but only on request. The default output stays out of the way.

## The three dialects

| | Krea 2 | MOSS-Image | Qwen-Image 2.1 |
|---|---|---|---|
| Prompt form | 1 to 2 paragraphs, 80 to 350 words | caption, or Layout JSON with boxes | T2I: one paragraph, about 450 words. Edit: one instruction paragraph |
| Text in the image | unreliable past about five words | reliable, boxes pin placement | strongest, every legible string quoted |
| Editing | masked edits, prompt describes the whole frame | not an editor | native instruction edits on one or many images |
| Reference images | `Picture N`, three maximum, 0.25 layout split | described in the caption | `<image1>`, `<image2>` tags |
| Negatives | inert at CFG 1.0, positive constraints instead | no negative field | no negative field |
| Default settings | 8 steps, CFG 1.0, euler, shift 4.0 + beta | model card, dimensions in multiples of 16 | Lightning 8-step at CFG 1.0 |

## Layout

```
.agents/skills/image-prompt-forge-comfyui/
  SKILL.md                  brief, scene graph, routing, output contract
  references/
    krea2.md                prose shape, constraints, tested failure table, inpaint
    qwen-2-1.md             eight-step T2I paragraph, edit protocol, ratio payload
    moss.md                 caption vs Layout
    vocabulary.md           camera, light, material, style formulas, genre recipes
    settings.md             sampler and resolution defaults per family
    layout.md               MOSS bounding-box draft rules
    poses.md                eleven-field pose schema, hands and held objects
    pose-library.md         30 prepared pose blocks
    adult.md                18+ register and failure table, loaded only for adult briefs
  scripts/
    compile_prompt.py       MOSS Layout draft to compiled prompt
    test_compile_prompt.py  8 tests, no dependencies
```

## Editing the skill

`SKILL.md` is the router: it reads the brief, builds a scene graph, picks the model, then loads exactly one dialect reference. Model rules live in one file each, so changing Krea behaviour means editing `references/krea2.md` and nothing else.

The description in the frontmatter says only when to use the skill. It deliberately does not summarize the workflow, because an agent that reads a summary will follow the summary instead of the file.

Run the tests after touching the compiler:

```bash
cd .agents/skills/image-prompt-forge-comfyui/scripts
python -m unittest test_compile_prompt
```

Check that every reference path still resolves:

```bash
cd .agents/skills/image-prompt-forge-comfyui
for f in $(rg -o 'references/[a-z0-9._-]+\.md' SKILL.md | sort -u); do [ -e "$f" ] || echo "missing $f"; done
```

## Archive

`.agents/archive/` holds the eight skills this one was distilled from. Git ignores it, so it stays on disk and out of the published repo. It sits outside skill discovery, so those skills will not load by accident. Editing them changes nothing; they are there to be read when a rule in `image-prompt-forge-comfyui` needs tracing back to where it came from.

## Licence

The text and code here are licensed [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). The full text is in [`LICENSE`](LICENSE). That covers the prompt-writing material and the layout compiler. What you do with the prompts, and what each model's own licence lets you do with the images, is outside its scope.

Share-alike applies because the biggest source is under the same licence. If you reuse this skill, credit it and license your version the same way.

### Attribution

This skill merges and rewrites other people's work:

- [`rakshazi/krea2-prompting`](https://github.com/rakshazi/krea2-prompting), CC BY-SA 4.0. The Krea 2 rules, tested failure table, pose system and reference-image guidance come from here.
- [`buxinzi2233/krea2-prompting`](https://github.com/buxinzi2233/krea2-prompting), MIT. The basis for the skill above.
- [`AI-KSK/krea2-prompt-suite`](https://github.com/AI-KSK/krea2-prompt-suite). The K2-SCENE prompt architecture and the reverse-prompting procedure.
- Alibaba's published Qwen-Image 2.1 prompt-rewriting specification, which `references/qwen-2-1.md` follows.
- The MOSS Layout schema and `scripts/compile_prompt.py` were carried over from this repo's earlier `moss-image-prompt-enhancer` skill.
- The pose system is credited to [@Zuanfilm](https://x.com/Zuanfilm) in `references/poses.md`.

What changed: the sources were merged into one skill with a single routing layer, their ComfyUI workflow wiring was dropped, settings were cut back to family defaults, and the Qwen dialect now writes English prose throughout. The unmodified sources are kept locally in `.agents/archive/`, which git ignores.

## Caveats

- No render verification. The prompt rules come from the archived skills' own testing notes and from published model documentation, not from a fresh bake-off.
- Settings are family defaults. A finetune can ship its own encoder, VAE, sampler or shift; take those from its model card.
- `references/adult.md` is 18+ material and is only loaded when the brief asks for it.
