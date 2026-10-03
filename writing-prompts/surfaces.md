# Surfaces — in depth

Attachment to `SKILL.md`. The Step 1 table is enough to classify the surface; this file serves to write the prompt once the surface is classified. Open only the section of the target surface.

## Chat on claude.ai

Text levers only. No effort, thinking config or sampling parameters: mentioning them is noise. The separation between instructions and input is done with XML tags inside a single block.

The available levers are those of family A in `SKILL.md` — directive, operational definition, context on the goals, output format, role, style instructions, scope — plus exemplars and input handling (families B and C in `SKILL.md`) when needed. The rest of the taxonomy presupposes several calls: in chat a chain is done through successive turns of the same conversation, so it is a work plan to declare, not a technique internal to the single prompt.

Since there is no sampling to tune, variety between two answers is obtained by varying the prompt and not the parameters.

## Claude Code

To be assessed one by one: every block included without a reason is one more constraint to read.

- **Against over-engineering**: no features, refactors or improvements beyond what was asked; no docstrings or comments on untouched code; no error handling for impossible scenarios; no abstractions for one-off operations.
- **Against hardcoding**: a general solution valid on all valid inputs, not only on the tests; tests verify correctness, they do not define the solution; if the task is infeasible or a test is wrong, say so instead of working around it.
- **Against hallucinations about code**: no claims about files not opened, read the relevant files before answering.
- **Destructive actions**: local, reversible actions are free; confirmation before operations that are hard to undo or visible to others, that is force push, hard reset, deletions, push, PR comments. Never use destructive shortcuts to get around an obstacle.
- **Temporary files**: if it creates some to iterate, clean them up at the end of the task.
- **Subagents**, especially on Opus 5: delegate only for large, genuinely independent work; do not delegate what closes in a few tool calls; do not use subagents to verify its own work; keep the number of spawns low.
- **Parallel calls**: independent tool calls in parallel, sequential only when one depends on the result of the previous.
- **Long task across several context windows**: tell the model that the context will be compacted and that it must not close the work early for fear of running out of budget, saving the state before the refresh instead.
- **Non-existent packages**: generated code may import packages that do not exist, and that name may be registered by an attacker with malicious code inside (p. 30). In prompts that install dependencies, ask to verify that the package exists and is the expected one.

## Reusable system prompt

Instantiated on different inputs: it needs explicit placeholders, a constrained format and, if code reads the output, answer engineering (family I in `SKILL.md`).

How it is structured.

- **Placeholders.** The variable input goes in its own XML tag, with a name that says what it contains and stays the same in all instances of the prompt. The rule on XML tags in `SKILL.md` applies here more than anywhere, because it is the only boundary between the fixed instructions and the content that changes with every call.
- **Role.** One line that sets expertise and register, at the top of the system prompt.
- **Constrained format.** Form, fields and length declared once in the prompt, not renegotiated at every instance.
- **Exemplars.** If you put some in, the order is fixed: it is a reused prompt, and on some tasks accuracy varies from below 50% to over 90% just by changing the order (family B in `SKILL.md`, design decisions in `techniques.md`).
- **Closing reminder.** On Opus 5, in a long system prompt, the main instruction is paired with a reminder towards the end; the form is in `models.md`.

A system prompt is L2 by definition, so the friction of an interview question is amortized over many runs: here the Step 2 interview almost always pays off.

## API calls

Here `effort`, `thinking`, `max_tokens` and structured outputs exist. The parameter block goes in only here.

The concrete values are in `models.md`, the «API only» line of the Sonnet 5 delta: default effort, sampling parameters that return 400, manual extended thinking, and the new tokenizer that inflates counts and can make `max_tokens` values inherited from an old project truncate. Do not copy them here: if they change, they change in one place only.

Two practical consequences for the text of the prompt.

- **No prefill.** From the 4.6 models onward it returns a 400 error. To remove preambles, ask for it as an instruction, have the output produced inside XML tags, or use structured outputs.
- **Output consumed by code.** The answer engineering of family I in `SKILL.md` applies: answer shape, answer space, verbalizer, extractor, label names. On structured outputs the Anthropic documentation wins over the paper, and the divergence is explained at the end of `techniques.md`.

- If the prompt will live long on an API, remember that the model behind it changes over time and the result with it (prompt drift, p. 31): a comment line with the date and the model it was tuned on pays for itself.
