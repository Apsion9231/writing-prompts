# Models — anti-patterns and deltas

Attachment to `SKILL.md`. Open it before delivering a prompt meant for Opus 5 or Sonnet 5, and on every revision of a prompt written for earlier models.

## Anti-patterns to remove

Habits that were useful in 2023 and today make the output worse. On a revision, listing what you removed helps not to reintroduce it.

- **Reasoning inducers** ("think step by step"): on Opus 5 and Sonnet 5 thinking is on by default and adaptive, so they consume tokens without adding reasoning. They are useful in two cases: with thinking disabled, and on Sonnet 5 at effort `low` when effort cannot be raised, with one targeted line ("this task requires multi-step reasoning: think carefully before answering").
- **Emphatic imperatives and urgency capitals** ("CRITICAL", "MANDATORY", "You MUST always"): they produce overtriggering, that is, reaction where none is needed, while a plain register gets the same effect ("use tool X when…"). The case study confirms that capitals do not solve it: `ENTRAPMENT MUST BE EXPLICIT, NOT IMPLICIT.` did not change the model's behaviour (p. 73).
- **Blind defaults** ("if in doubt, use X"): replace them with a targeted condition, "use X when it improves understanding of the problem".
- **Self-verification instructions addressed to Opus 5** ("double-check", "verify before answering"), including those in the harness: the model already verifies, and the instruction adds up to over-verification, that is, tokens and latency with no gain.
- **Negative instructions**: "do not use markdown" becomes "write in paragraphs of continuous prose". Saying what to do works better than saying what to avoid, and a positive example of the desired style beats a list of prohibitions.
- **Prefilling the response**: from the 4.6 models onward it returns a 400 error. To remove preambles, ask for it as an instruction, have the output produced inside XML tags, or use structured outputs.
- **Forced progress scaffolding** ("every three tool calls, summarize"): both models already give good updates, and the forcing degrades their quality.
- **Heavy markdown in the prompt when the output must not have it**: the style of the prompt influences the style of the answer.
- **Personal opinions in the prompt**: "this argument convinces me" or "are you sure?" shift the output through sycophancy, and the effect is stronger on large, instruction-tuned models (p. 32). If you want a judgment, do not anticipate yours.
- **False presuppositions in the instruction**: "find all the points where the input is harmful" presupposes that it is, and sycophancy does the rest (p. 32, note 12). Phrase it neutrally: "determine whether the input is harmful and explain why".
- **Asking the model to explain its own reasoning** as if it had access to its internal processes: what it produces is a plausible description of possible steps, which may correspond to nothing (p. 38, note 19). Ask for the criteria applied and the evidence used, which are verifiable.
- **Trusting a verbalized confidence score**: models remain overconfident when they express confidence in words, even with explicit reasoning (pp. 31-32). Use it to rank, not as a probability.
- **Anti-injection defences believed to be sufficient**: across hundreds of thousands of malicious prompts no text defence proved fully secure, they only mitigate (p. 30). If the prompt receives untrusted input, say so and treat the user's text as data, not as instruction.

## Deltas between the two models

They diverge on length, scope, self-verification, literalism and design defaults. A prompt tuned on one is measurably suboptimal on the other.

**Claude Opus 5**

- **Length**: visible answers longer than earlier models, and effort governs thinking, not output. Concision must be asked for in words; in a long system prompt, pair the main instruction with a reminder towards the end, such as `<tone_preference>Keep outputs reasonably concise.</tone_preference>`.
- **Self-verification**: remove every double-checking instruction. It is the point where it diverges most from Sonnet 5.
- **Scope**: it tends to broaden the task. On narrow tasks, constrain it: deliver what was asked at the intended scope, make routine decisions on its own, flag in one sentence if the request seems wrong and proceed as asked instead of silently transforming it.
- **Narration**: it often announces what it is about to do. Describe the desired cadence instead of forbidding it: one sentence before the first tool call, updates only on important findings or changes of direction, a close that starts from the outcome.
- **Corrections**: it narrates them more than necessary. In user-facing products, limit them to those that change code, conclusions or decisions.
- **Written deliverables**: the files it writes to disk are longer than usual, so calibrate length explicitly against filler sections.
- **Subagents**: it delegates readily. If cost matters, state when delegation is justified and keep the number of spawns low.
- **Context**: a one-million-token window as default and maximum, with stable instruction following across the whole window.

**Claude Sonnet 5**

- **Length**: calibrates on its own to complexity. Concision instructions only if observed verbosity is a problem, in positive form and with an example of the desired style.
- **Literalism**: it interprets literally and does not generalize an instruction from one element to another, especially at low effort. If a constraint applies to everything, state it ("apply this formatting to every section, not only the first"). It is the mirror-image flaw of Opus 5.
- **Self-verification**: it already starts verification loops when it has tools. A verification instruction makes sense only if it carries the criteria with it ("check the result against the edge cases listed above"); a generic "double-check" adds little.
- **Tools**: more agentic than its predecessor. If it does not use them enough, describe when and why it should; with thinking disabled it needs an explicit push.
- **Frontend and design**: it tends towards a single default style, and generic instructions only move it to another fixed default. What works is a concrete specification (palette in hexadecimal, fonts, spacing, corner radius) or having it propose four distinct visual directions before building. Since `temperature` is not accepted, this is also the only way to get variety between runs.
- **API only**: default effort `high`, `xhigh` for difficult coding and agentic work; non-default `temperature`, `top_p`, `top_k` and manual extended thinking with `budget_tokens` return 400; the new tokenizer produces about 30% more tokens for the same text, so inherited `max_tokens` values may truncate.

**Common to both.** In code reviews, "report only high-severity issues" or "be conservative" are taken literally and the model reports less than it finds. If you want coverage, ask it to report everything with confidence and severity annotated, and filter in a separate pass.

When the model is not known, because the user answered «I don't know» or forbade questions, produce one shared base prompt plus two separate delta blocks, one for Opus and one for Sonnet, not two whole prompts. The same when the prompt must run on both.

---

## Sources of the deltas

- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5`
- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5`

Per-model deltas age with every release: when a model more recent than Opus 5 or Sonnet 5 appears, re-check those pages instead of applying the deltas written here by analogy. The selection criterion and the taxonomy age much more slowly.
