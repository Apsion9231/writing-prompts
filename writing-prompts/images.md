# Images — prompts for generators and for editing

Attachment to `SKILL.md`. Open it when the prompt must generate or edit an image, with any generator: inside a chat (ChatGPT, Gemini) or in a dedicated tool. Tables A-I of `SKILL.md` are not used here, because they are designed for text models; the interview, the parsimony criterion and the delivery remain valid.

No version names and no per-vendor cases. Where generators behave differently, the rule is written as a condition on what the generator offers.

## The ten slots, for an image

The Step 2 interview does not change: same slots, same order, one question per empty slot. What they mean changes.

| Slot | For an image |
|---|---|
| 1. Form of the artifact | Aspect ratio, orientation, resolution, how many variants; where the image ends up (cover, print, post, background) |
| 2. Perimeter | What is in the scene and what is not; generation from scratch or editing of an existing image |
| 3. Domain definitions | The style the user named in words ("editorial", "clean", "vintage"): what it means in visual terms |
| 4. Method and tools | Which generator, and which native controls it offers: aspect ratio, negative field, mask, reference images |
| 5. Thresholds and numbers | How much text in the image, how many subjects, margins for an overlaid title |
| 6. Fields and content | Subject, action, place, composition and framing, light, optics, materials, palette |
| 7. Recipient | The generator; if not deducible, it is the first question |
| 8. Consumer | Who looks and where: website, print, social media, commercial use |
| 9. Empty result and false premise | In an edit: what must stay identical. In general: what to do if a constraint cannot be achieved (text too long, inconsistent reference) |
| 10. Case data | The real subject the image must reproduce (the real product, place, person, brand) and whether the user has a photo of it |

## The paper's techniques for images

The paper devotes a short section to images (§ 3.2.1, p. 22). For this branch they are the equivalent of tables A-I: call them by their name in the techniques line.

| Technique | What it does | Goes in when | p. |
|---|---|---|---|
| Prompt Modifiers | Words that change the rendering: medium ("oil on canvas"), light, optics, style | Almost always: they are the vocabulary of slot 6 | 22 |
| Negative Prompting | Weighs negatively terms to avoid | Only if the generator has a negative field or syntax; otherwise see the exclusions below | 22 |
| Paired-Image Prompting | Shows a before/after pair and asks for the same transformation on a new image | Repeated editing with the same effect on several images, with a generator that accepts input images | 22 |
| Image-as-Text | Describes a reference image in words and uses the description in the prompt | The generator does not accept references, or you need to fix what matters in the reference | 22 |

## Principles the guides converge on

1. **Concrete beats generic.** Subject, materials, colour, light and composition named. "Beautiful" and "professional" carry no visual information.
2. **Sentences, not keyword lists.** One coherent descriptive paragraph, as long as needed to fix the elements that matter; lengthen only to add constraints that really change the image.
3. **Stable order**: subject and action, then place and context, then composition and style, finally the constraints. Open with the verb that declares the operation: generate, edit, replace.
4. **Photographic and cinematic vocabulary**: shot, camera angle, focal length, aperture and depth of field, lighting setup, colour temperature, grain.
5. **Describe what you want.** "Deserted street" instead of "without cars": naming the object to exclude tends to make it appear.
6. **Text in the image in quotation marks**, as short as possible, with font, colour and position described. The user checks the spelling on the result: tell them in the delivery, outside the prompt, because asking the generator does not make it reread it.
7. **Start simple and change one thing at a time.** Short follow-ups ("keep everything, warm up the light") are the intended mechanism, not a fallback.

## Editing and references

- **Say what changes and what stays identical**: identity of the subjects, light, perspective, composition, style. "Change only the sign; everything else in the image stays exactly as it is." Repeat the preservation constraint at every round, because every round can shift it.
- **Several reference images: one role for each**, naming or numbering them ("take the face from the first, the light from the second").
- **Even with a mask**, write in the text what changes inside the area and what is preserved: on several generators the mask is an indication, not an exact boundary.

## Where generators diverge: the conditions

| Aspect | If the generator… | Then |
|---|---|---|
| Exclusions | has a negative field or a weighting syntax | First rewrite the scene in positive terms; if an unwanted element remains, put it in the field as a bare noun, without "no" |
| | does not have one | Exclusion written in positive terms within the sentence; a short exclusion sentence only if the positive form does not exist |
| Aspect ratio | has a native control (parameter, menu) | Tell the user in the delivery, outside the prompt, and do not repeat it in the text in a different form |
| | does not have one | Aspect ratio and orientation written in words in the prompt |
| Automatic prompt rewriting | applies it | If precise control is needed, tell the user to turn it off or to reread the rewritten version |
| Text in the image | renders long text badly | Text to a minimum; complex typographic compositions are done outside the generator |

If the generator is not known, write the positive version with the aspect ratio in words: it holds everywhere.

## Outside the prompt, for the user

Many generators mark the images they produce with invisible watermarks or content credentials. If the image is meant for a website, sales material or commercial use, say so in the delivery.

---

## Sources

Paper: Schulhoff et al., *The Prompt Report*, § 3.2.1, p. 22.

Official guides, consulted on 13 September 2026:

- `https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide` — OpenAI guide to prompting for image generation (updated 21 April 2026)
- `https://developers.openai.com/api/docs/guides/image-prompting`
- `https://developers.openai.com/api/docs/guides/image-generation`
- `https://ai.google.dev/gemini-api/docs/image-generation`
- `https://docs.cloud.google.com/vertex-ai/generative-ai/docs/image/img-gen-prompt-guide` — Google guide to image prompts (updated 9 September 2026)
- `https://cloud.google.com/blog/products/ai-machine-learning/ultimate-prompting-guide-for-nano-banana` — Google Cloud, 6 March 2026
- `https://docs.bfl.ai/guides/prompting_summary` and `https://docs.bfl.ai/guides/prompting_unified_basics` — Black Forest Labs
- `https://docs.ideogram.ai/using-ideogram/generation-settings/negative-prompt` — Ideogram

Not opened (403 response), used only through search-result snippets and therefore as confirmation, not as a source: the Midjourney documentation (`docs.midjourney.com`) and the Adobe Firefly prompt guide (`helpx.adobe.com`).

Recent academic literature on image prompting: not yet consulted. When it comes in, it goes in this section, and where it contradicts a principle above it is decided case by case.
