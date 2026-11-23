# Settings

Family defaults only. Load the model card for a finetune that ships its own encoder, VAE, shift or sampler. Never carry numbers across model families: FLUX.1 Krea [dev], Krea 1, Qwen 2512 and Qwen 2.1 do not share a sampler table.

## Krea 2 Turbo (ComfyUI)

| Setting | Value | Note |
|---|---|---|
| Steps | `8` | Distilled to 8; more steps grow noisier |
| CFG | `1.0` | The official CLI says 0.0; in a KSampler literal 0.0 is pure unconditional output and breaks the image. Above ~1.5 it oversaturates |
| Sampler | `euler` | Holds across tested combinations |
| Scheduler | `beta` for illustration, either for photography | `beta` adds garment folds, hair strands, fur; `simple` gives flatter fills |
| Shift (mu) | `4.0` smooth and slim, `1.15` compact and grainy | Style dial, independent of scheduler |
| Resolution | native `2048`, minimum `1024`, divisible by 16 | Trained at 2048; avoid gen-at-1024-then-upscale |
| Text encoder | `qwen3vl_4b` | fp8_scaled lighter, bf16 more precise |
| VAE | `qwen_image_vae` | Check this first when colour or fine texture is off |

Ratios: `1:1` product/portrait, `16:9` cinematic hero, `21:9` panoramic, `2:3`/`9:16` poster, `4:3` environment.

## Krea 2 RAW

Base checkpoint for fine-tuning and LoRA training; official guidance currently 52 steps, CFG 3.5, up to ~1K. Treat as implementation-specific, not a law. This is the path where a negative prompt is live.

## Qwen-Image 2.1

| Preset | Steps | CFG | Sampler | Scheduler | LoRA |
|---|---|---|---|---|---|
| Lightning 8-step | 8 | 1.0 | euler | simple | Lightning-8steps @1.0 |
| Lightning character | 8 | 2.5 | euler | simple | Lightning-8steps @1.0 |
| Lightning 4-step | 4 | 1.0 | euler | simple | Lightning-4steps @1.0 |
| Standard | 50 | 4.0 | euler | simple | none |
| Character composition | 30 | 4.0 | euler_ancestral | beta | none |

Lightning has the shift baked in; the standard path wants `ModelSamplingAuraFlow shift 3.1`. Qwen runs natively at ~1.6 MP: `1328×1328` square, `1056×1584` 2:3, `1664×928` 16:9, `1104×1472` 3:4, `1536×2048` tall. Edit target size equals the source and stays at or under ~1 MP per pass.

## MOSS-Image

Width and height are positive multiples of 16 and must match the generation workflow exactly. No sampler advice here — follow the model card. Default canvas when nothing is given: `1024×1024`, or the shape the medium implies.

## LoRA behaviour

- Krea: a slider that seems dead may need ±3–5 at a fixed seed before you write it off. Colour bias scales with strength — 0.7 often reads paler than 0.9 while holding the shape.
- Krea inpaint: the edit adapter is required; stock `SetLatentNoiseMask` partial denoise collapses form.
- Qwen Lightning: shift is baked in, do not add AuraFlow on top.
- Reference/style LoRAs: lower strength when the reference's texture bleeds into the whole frame.
