# Visual vocabulary

Shared across the three models. The point is to name a setup rather than an adjective: `photorealistic` states a preference, `85 mm, f/1.4, soft rim light, visible pores` gives a renderer something to build. These terms are useful in all three dialects; the Krea-specific failure rules live in `references/krea2.md`.

## Camera

**Shot size:** extreme close-up, close-up, portrait, three-quarter, half-body, full body, wide establishing shot.

**Height and angle:** low angle, high angle, eye level, top-down, dutch tilt, over-the-shoulder.

**Lens feel:** `35mm environmental`, `50mm natural`, `85mm portrait`, `f/1.4`, `f/2`, shallow depth of field, deep focus, creamy bokeh, long-lens compression, macro, wide-lens distortion.

**Film stock as palette control:** Kodak Portra 400 (warm), Kodak Gold, Fuji Pro 400H, Cinestill 800T (tungsten night), Ilford HP5 (black and white). `film grain` pulls the whole frame toward analog editorial.

**Register:** `candid, in the moment` · `taken on a phone camera` (casual snapshot) · `amateur photo` + `harsh direct flash` (gritty candid; put the register clause first or it is ignored).

## Light

Always: source + direction + quality + shadow behaviour + highlight behaviour + any secondary source.

- **Quality:** soft diffused, hard directional, flat even ambient, dappled, hazy.
- **Time:** golden hour, blue hour, overcast, midday, dusk.
- **Direction and role:** rim light, backlight, side light, top light, underlight, motivated practical (a window, lamp, neon, screen), low-key, high-key, warm tungsten, cool daylight.
- **Realism cue:** name the visible source. Light with no source reads as a studio default.

Skin is decided by light before words: flash pushes warm red into skin. Pale, even or matched tone → `soft overcast daylight, neutral white balance` or `cool diffused daylight from a north window`. Then anchor the tone to a named body part (`the same pale tone as her thighs`) before naming the complexion.

## Texture and imperfection

The anti-plastic set: natural skin texture, visible pores, vellus hair, fabric weave, wet highlights, condensation, dust in a light beam, micro-reflections, wet pavement reflections, scuffed surfaces, worn edges, fingerprints on glass, stray hairs, freckles, weathered skin.

For a tight face crop, the beauty prior wins unless the anchors sit in the same sentence as the face: `natural facial features, small dark eyes with hooded lids, uneven skin with visible pores and fine vellus hair, slight under-eye shadows`.

**Materials behave under light.** Say what the surface does: brushed aluminium with narrow linear highlights, matte unglazed ceramic with chalky absorption, translucent silk with edge glow, polished black marble with controlled reflections, fibrous handmade paper with imperfect ink absorption.

## Colour

Dominant / secondary / accent; saturation level; brightness range; warm-cool relation; contrast. Restrained palettes read more photographic than rainbow ones. Name colour with a modifier — deep navy, muted olive, pale cream, warm terracotta, charcoal, off-white, dusty rose.

## Style

Two ways to get a named look.

**Formula A, "built on"** (most styles):

> A `<3 mood adjectives>` `<medium>` style built on `<linework / mark-making>`, `<shading / tonal treatment>`, `<proportion or form logic>`, `<atmosphere>`, and `<a summarising intensity that feels Y>`. Influenced by: `<tradition>`, `<technique>`, `<school or studio practice>`, `<method>`.

**Formula B, "characterized by"** (artist-anchored):

> `<medium>` aesthetics characterized by `<trait>`, `<trait>`, `<trait>`, blending `<A>` with `<B>` through `<mechanism>`, **reminiscent of** `<Artist 1>` and `<Artist 2>`, **inspired by** `<Work 1>` and `<Work 2>`.

The trailing attribution clause is the strongest signal in the paragraph — the encoder knows those names. If a style is not landing, strengthen that clause first.

Rules: one style per prompt (two paragraphs of surface description compete); match the style family to the subject (anatomical pose prose fights `8-Bit Style`); delete an unwanted clause rather than negating it; weight only the opening phrase, and only with a weighting node.

## Diegetic fantasy

Impossible anatomy in a photoreal frame needs a costume frame or it reads as illustration: `realistic furry cosplay cat ears with pink inner fur`, `a fluffy cosplayed cat tail`. Bare `cat ears` float. Frame the impossible as wardrobe.

## Genre recipes

**Portrait** — `A [shot] portrait of [subject] in [wardrobe], [setting], [lighting], [lens], [skin texture], [grade], [crop].`

> A calm editorial portrait of a young woman at golden hour, soft rim light, shallow depth of field, 85mm f/1.4, natural skin texture with catchlights, pastel wardrobe, 3:4 crop.

**Product** — name the surfaces; the backdrop is a material, not a colour.

> A matte ceramic skincare jar on a sculpted stone plinth, continuous warm-grey sweep backdrop, soft directional key light with a gentle rim highlight, 4:3 hero crop.

**Landscape** — `Establishing view of [place], [weather], [light path], [scale cue], [lens], [palette], [crop].`

> A rust-orange suspension bridge across deep blue-green water, low vantage point, long-lens compression, crisp midday light, 16:9.

**Architecture** — building type + materials + context + light + medium.

> Modern residential house, white stucco facade, large glass windows, concrete base, mature trees, warm late-afternoon sun, architectural photography.

**Street** —

> Candid street photograph of a courier in a rain-slicked city on an overcast day, 35mm f/2, documentary realism, muted tones, wet pavement reflections, motion in the background crowd, film grain.

**Landing-page hero** — two extra rules: reserve a quiet zone (`wide empty sky on the left third for overlaid title text`; the model fills every region unless told otherwise) and set the crop up front, before styling.

> A rain-slicked city alley at night, neon reflections on wet asphalt, low camera angle, cinematic wide shot, generous dark sky in the upper left clear for a headline, 16:9.
