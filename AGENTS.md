# Working in this repo

One agent skill, `image-prompt-forge-comfyui`, for writing image prompts for Krea 2, MOSS-Image and Qwen-Image 2.1. The skills it was distilled from sit in `.agents/archive/` as source material.

It produces text only. No renders, no ComfyUI API calls, no image files.

## Map

| Path | What it is |
|---|---|
| `.agents/skills/image-prompt-forge-comfyui/SKILL.md` | the router: brief, scene graph, model choice, output contract |
| `.agents/skills/image-prompt-forge-comfyui/references/` | one dialect file per model, plus shared vocabulary, settings, poses, layout, adult |
| `.agents/skills/image-prompt-forge-comfyui/scripts/` | MOSS Layout compiler and its tests |
| `.agents/archive/` | the eight superseded skills, read-only source, git-ignored |
| `.codegraph/` | local tool data, git-ignored |

## Invariants

1. **The frontmatter description says when to use the skill, never what it does.** It starts with "Use when" and carries the trigger words: model names, ComfyUI, prompt, poster, reference image, edit, inpaint, reverse prompt. An agent that reads a workflow summary follows the summary instead of the file.
2. **Model rules live in exactly one reference file.** Cross-model facts belong in the dialect table in `SKILL.md` and nowhere else. If a rule changes, it changes in one place.
3. **Every path named in `SKILL.md` must resolve.** Run the check below before committing.
4. **Prompt prose is English**, always, whatever language the request arrives in. Text rendered inside the image stays character-exact in its own script and is never translated or re-capitalised.
5. **`references/adult.md` is 18+ and isolated.** It is loaded only for explicitly adult briefs. Do not link it from the general workflow, do not summarize it in `SKILL.md`.
6. **Settings stay at family defaults.** No checkpoint filenames, no local paths. State sampler, steps, CFG, shift, LoRA and resolution for the family, and tell the reader to take a finetune's own values from its model card. This was a deliberate choice; do not "helpfully" add machine-specific detail.
7. **No quality-stack advice and no negation advice for CFG-1 models.** `masterpiece, 8K` buys nothing, and `no people` is inert at CFG 1.0. Constraints are written as visible positive states.
8. **The output contract is stable**: prompt block, one settings line, one assumption line. Variants and scores only when asked.
9. **`.agents/archive/` is read-only and local.** Git ignores it and it sits outside skill discovery. Edits there change nothing and desync it from the published source skills.

## Checks

```bash
# compiler tests (8, no third-party dependencies)
cd .agents/skills/image-prompt-forge-comfyui/scripts && python -m unittest test_compile_prompt

# every reference path resolves
cd .agents/skills/image-prompt-forge-comfyui
for f in $(rg -o 'references/[a-z0-9._-]+\.md' SKILL.md | sort -u); do [ -e "$f" ] || echo "missing $f"; done
```

## Adding a fourth model

1. Write `references/<model>.md` with its form, language, text reliability, edit behaviour, ratio handling and settings. Keep it to what actually differs.
2. Add a row to the models table and a row to the dialect table in `SKILL.md`.
3. Add a route row so the model is chosen for the right kind of brief.
4. Add a settings block to `references/settings.md`.
5. Add a line to the references table at the bottom of `SKILL.md`.
6. Run the path check.

If the new model's answers contradict an existing rule, the rule belongs in the model's own file, not in the shared sections.

## Installing

The skill is a plain directory. Copy it to `~/.agents/skills/` for global use or a project's `.agents/skills/` for one repo. No build step, no package manager, no symlinks required.
