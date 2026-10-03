# Other AIs — calibration for non-Claude text models

Attachment to `SKILL.md`. Open it before delivering a prompt meant for ChatGPT, Codex, Gemini or any text model that is not Claude, and on every revision of a prompt written for them. For prompts that generate or edit images, open `images.md` instead.

This layer is deliberately thin. Technique selection, the interview and the gates of `SKILL.md` apply here as on Claude: only what follows changes. There are no per-vendor cases and no version names, because they age faster than this text. If a calibration choice is decisive for the result and depends on how a specific model behaves today, check the vendor's current guide instead of applying these lines or the deltas of `models.md` by analogy.

## Proportionate structure

Vendors disagree on structure. One guide recommends starting from the desired result and keeping the prompt short; another recommends consistent delimiters, XML-style tags or Markdown headings, with critical instructions at the top. The rule that holds on both is parsimony applied to form:

- **Delimiters only where they separate something.** Pasted material and variable input go in a delimited block, because it is the only boundary between what the model must execute and what it must read. Labelled sections, roles in a tag, instructions split into five blocks: no.
- **One delimiting system per prompt**, tags or headings, never mixed.
- **Start from the result.** Describe what must arrive and what it is for; describe the steps only when the process itself is a constraint.
- **Long material first, request at the end**, with a sentence linking them ("based on the text above…"). On this the guides agree.

## Surface

The default is the web chat or the app: no parameters, no sampling, no reasoning levels to set. Mentioning them in the prompt is noise. If the prompt goes to an API, say so in the delivery and point out that parameters are set outside the text.

The user's cross-cutting preferences (language, tone, usual format) on these products live in the account's personalization settings, not in the prompt: the prompt keeps what is specific to the task.

## What must be asked for in words

- **Length and depth.** Some models answer concisely by default: if reasons, alternatives or a long text are needed, it must be written.
- **Grounding, in one of the two directions.** If the answer depends on current facts: search and cite the sources. If the task is working on supplied material: stick to the material and state when a piece of information is not there. A prompt that does not choose leaves the model the choice between inventing and searching.
- **Small models and free tiers.** The guides are written for the full models. With a smaller model, or when you do not know which one will answer, being explicit weighs more: format, acceptance criteria, what to do if a datum is missing.

## Cross-checking

A recurring use: having another AI check work done elsewhere.

- **Ask for errors, not confirmations.** "Check that it is correct" induces agreement; "list the factual errors, the steps not supported by the material and the claims you cannot verify" does not. It is the same sycophancy as false presuppositions in `models.md` (p. 32), seen from the opposite side.
- **Present the text neutrally.** Do not say that another AI wrote it nor that the user wrote it: the two attributions shift the judgment in opposite directions.
- **Give the checker the criterion**, not just the text: wrong with respect to what (the supplied article, a standard, a dataset).

## Anti-patterns to remove

- **Claude style carried over.** Decorative XML tags, multiple sections, an elaborate role, redundant verification instructions. It is the most likely defect of a prompt written with this skill, because the rest of the skill is optimized for Claude.
- **Reasoning inducers** ("think step by step", "reason before answering"): current models from the main vendors already reason internally. On truly hard problems, one line asking it to think deeply is enough.
- **Emphasis and urgency capitals** ("IMPORTANT", "MANDATORY", "NEVER… EVER"): a plain register gets the same effect. The paper's case study measures it: capitals did not change the behaviour (p. 73).
- **Negative-only instructions**: "do not invent" becomes "if a piece of information is not in the text, write that it is missing".
- **All sources attached just in case**: only the material that can change the answer, saying what to take from each piece.
- **Ten constraints**: one or two, those that, if violated, make the result unusable.

---

## Sources

Consulted on 13 September 2026:

- `https://learn.chatgpt.com/docs/prompting` — OpenAI guide to prompting for ChatGPT and Codex: start from the result, short prompts.
- `https://developers.openai.com/api/docs/guides/prompt-guidance` — OpenAI guide to the current model via API.
- `https://ai.google.dev/gemini-api/docs/prompting-strategies` — Google prompting strategies, updated 10 June 2026: consistent delimiters, critical instructions at the top, long context first and question at the end.

Cross-checking, the free tier and the anti-patterns come from an earlier version of this skill (sources consulted on 31 August 2026), made generic. Vendor guides change with every release and in the last two years have also changed direction: re-check them before relying on a detail.
