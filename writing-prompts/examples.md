# Examples — before and after

Attachment to `SKILL.md`. Four pairs, one per surface, built on the anti-patterns listed in `models.md`. They serve in revision: when you remove an anti-pattern, the pair for the surface in question shows the user what changes without having to explain it in words. They are not templates to copy: the task changes, the transformation stays.

---

## 1. Chat on claude.ai

Anti-patterns: reasoning inducers, emphatic imperatives and urgency capitals, negative instructions, personal opinions in the prompt.

**Before**

```
You are a communication expert. Think step by step before answering.
Analyse the text below. It is ESSENTIAL that you do NOT use bullet points,
do NOT use markdown and are NOT generic.
It seems badly written to me, do you agree?

[pasted text]
```

**After**

```
<task>
Determine whether the text in <text> works as the opening of a technical
newsletter read by analytical chemists, and explain the judgment.
</task>

<criteria>
The reader understands in two lines what it is about; specialist terms
are used with their precise meaning; it does not promise content that the
rest of the newsletter does not deliver.
</criteria>

<format>
Continuous prose, three paragraphs at most, no lists: the text ends up
inside an email and lists break it up.
</format>

<text>
[pasted text]
</text>
```

**What changed.** The inducer disappears, thinking is already on. The capitals had no effect anyway, and in their place there is a plain register. The three prohibitions become a positive prescription with the reason, so the model generalizes to cases not foreseen. The question "do you agree?" anticipated the judgment and shifted it through sycophancy: the request is neutral and the criteria are explicit.

---

## 2. Claude Code

Anti-patterns: blind defaults, self-verification instructions addressed to Opus 5, forced progress scaffolding, emphatic imperatives.

**Before**

```
Fix the bug in the CSV parser. You MUST ALWAYS double-check your work
before saying you are done. If in doubt, use subagents.
Every three tool calls write a summary of what you did.
Don't break anything.
```

**After**

```
In src/import/csv.py, rows with the separator inside quotes end up in
the wrong column. Fix the parsing and align the tests.

Scope: that file and its tests. No refactoring, no features beyond the
fix, no error handling for scenarios that do not occur.
The solution must hold on all valid inputs, not only on the test cases:
if a test is wrong, say so instead of working around it.
Delegate to a subagent only if you need to read a large, independent module.
If you create working files to iterate, clean them up before closing.
```

**What changed.** "Always double-check" produces over-verification on Opus 5, which already verifies: in its place is the real criterion, that is, what the fix must hold up to. "If in doubt, use subagents" was a blind default and becomes a targeted condition. The summary every three tool calls degraded the updates the model already gives well. The capitals give way to a declared scope, which is the constraint that was really needed.

---

## 3. Reusable system prompt

Anti-patterns: false presuppositions in the instruction, asking the model to explain its own reasoning, trusting a verbalized confidence score, labels the model does not cooperate in producing.

**Before**

```
You are an expert compliance assistant.
Find all the points where the customer's request violates the policy.
Explain your reasoning step by step.
Give a confidence score from 0 to 100.
If you are not sure, answer "cannot be determined".
```

**After**

```
<role>
You assess customer requests against the internal policy of a services
company. Dry register, no legal advice.
</role>

<task>
Determine whether the request in <request> complies with the policy in
<policy>. Report the criteria applied and the passage of text you base
it on, not the process by which you got there.
</task>

<policy>{{POLICY}}</policy>
<request>{{REQUEST}}</request>

<format>
A JSON object, nothing else:
{"outcome": "compliant" | "non_compliant" | "policy_does_not_cover_case",
 "criterion": "<policy clause>",
 "evidence": "<quote from the request>"}
outcome accepts only those three values.
</format>
```

**What changed.** "Find all the points where it violates" presupposed the violation and sycophancy did the rest: the wording is neutral and allows the negative outcome. Introspective reasoning becomes criteria and evidence, which are verifiable. The confidence score leaves the output, because it remains overconfident even with explicit reasoning and is not a probability; if a threshold is needed, it is done in a second Self-Calibration call. "Cannot be determined" was the label the model took refuge in: replaced by a label that says something precise and that it agrees to produce. The placeholders are in their own tags, with names that stay stable across instances.

---

## 4. API calls

Anti-patterns: prefilling the response, negative instructions, reasoning inducers, parameters no longer accepted.

**Before**

```python
messages=[
  {"role": "user", "content":
     "Classify the ticket. Think step by step. Don't add preambles, "
     "don't explain, don't use markdown. Answer ONLY with the label.\n\n" + ticket},
  {"role": "assistant", "content": "Label:"},
]
temperature=0.2
```

**After**

```python
system=(
  "You classify support tickets in order to route them. "
  "The output is read by a parser, not by a person."
)
messages=[
  {"role": "user", "content":
     "<ticket>\n" + ticket + "\n</ticket>\n\n"
     "Return the label inside <label></label>.\n"
     "Allowed values: billing, technical, sales, other."},
]
```

**What changed.** Prefilling the assistant turn returns 400 from the 4.6 models onward: the preamble is removed by asking for the output inside a tag, or with structured outputs if the parser sits downstream of a stable contract. `temperature` on Sonnet 5 returns an error, and removing it takes no determinism away from the result, because variety was governed by the prompt anyway. The inducer went away together with default thinking. The three prohibitions become a declared form plus a closed answer space, which is what the parser wanted. The extractor should look for the last occurrence of the tag, not the first.
