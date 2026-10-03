# Techniques — the numbers, the exclusions, the divergences

Attachment to `SKILL.md`. The selection tables of the nine families are in `SKILL.md`, because every run needs them. Here is what supports them and is rarely needed: the paper's numbers, the design decisions about exemplars, the operational details per family, the techniques left out with the reason, and the divergences between the paper and the Anthropic documentation.

Open it when the rationale line for a technique does not come to you, when the task is multi-stage, when you need the figure to let something in or keep it out, or when you are about to put exemplars into a reused prompt.

## Why few techniques

The paper catalogues 58 text techniques in six families (figure 2.2, p. 9), but those actually used are few: in the count of citations within its dataset, Few-Shot and Chain-of-Thought dominate, with a very long tail (p. 17). A skill that listed them all would produce bloated prompts.

The reason is empirical. In the paper's benchmark on MMLU with gpt-3.5-turbo (p. 33): Zero-Shot 0.627, Zero-Shot-CoT **0.547**, Zero-Shot-CoT with Self-Consistency 0.574, Few-Shot 0.652, Few-Shot-CoT 0.692, Few-Shot-CoT with Self-Consistency 0.691. Complexity does not pay off monotonically: one technique in six did worse than the baseline, and Self-Consistency, which triples the calls, did not improve the best variant. The authors declare the drops unexplained and define the choice of technique as a hyperparameter search (p. 34). Their recommendation to beginners is to start from the simplest approaches and stay sceptical of claimed performance (p. 45).

Parsimony does not, however, mean staying inside family A. It means climbing a step with a reason, choosing from the whole table: the right technique called by its name costs less than a hand-built paraphrase of it, because you know its cost and its evidence.

---

## Family A — the figure that supports it

The paper's most counter-intuitive figure concerns context on the goals: in the case study, removing from the prompt the email that explained the project's goals made F1 collapse by 0.27 points, from 0.45 to 0.18, and recall by 0.75 (p. 39). Explaining what the work is for paid off more than exemplars.

On the operational definition, the cost of getting it wrong is measured in the same case study: asked about the construct to be labelled, the model gave a definition different from the human coders', and from then on the definition was included in every prompt (p. 35). The definition that matters is that of whoever commissions the work, not the one the model considers reasonable.

## Family B — the six design decisions about exemplars

They apply every time you put in examples, and this is the part of the paper with the strongest numerical evidence (pp. 10-11).

**Order**: on some tasks accuracy varies from below 50% to over 90% just by changing the order, so in a reused prompt the order is fixed. **Quantity**: increasing, with benefits that may saturate beyond twenty examples, three to five being the balance point recommended by Anthropic. **Label distribution**: if unbalanced, it unbalances the output. **Label quality**: the literature is in conflict and large models tolerate inaccurate labels better, which is not a licence to put in wrong ones. **Format**: formats frequent in the training data perform better, and in the case study switching from `Q/R/A` to `Question/Reasoning/Answer` raised the share of unparsable outputs from 11% to 16% (pp. 40, 74). **Similarity**: exemplars close to the real case generally help, on some tasks diversity pays off more, so cover the edge cases.

In few-shot, generic instructions beat task-specific ones on classification and QA, and govern auxiliary attributes such as style more than correctness (p. 11).

## Family C — on repetition

The paper offers here the most honest anecdote in the literature: in the case study, context duplicated by mistake improved performance, removing the duplicate worsened it by 0.07 F1, but triplicating it did not help (pp. 40, 42). Repeating once the constraint that matters is cheap and sometimes useful, multiplying it is not.

`Question Clarification` (p. 32) deserves a note: it is the technique that makes the model ask before answering when the request is ambiguous. It goes into prompts meant for other people, where you cannot foresee how elliptical the incoming request will be. It is not needed in prompts you write for the user in front of you, because there you resolve the ambiguity at Step 2 instead of pushing it downstream.

## Family D — when code beats words

Program-of-Thoughts excels at mathematics and programming and is weak at semantic reasoning (p. 14): on a numerical task, having code written and executed is more reliable than having the calculation done in words.

## Family E — two warnings

In the benchmark, Self-Consistency improved only the zero-shot variant, not the best one (p. 33). In the case study, the ensemble over three orderings of the exemplars worsened F1 by 0.16, because two orderings produced unstructured outputs and required an extractor (p. 42): an ensemble also changes the form of the output, not only its quality. On Sonnet 5 `temperature` returns an error, so variety is obtained by varying the prompt, not the sampling.

## Family F — the distinction that matters on Claude 5

A chain across separate calls is legitimate, because it serves to inspect or filter an intermediate, and it is the chaining pattern the Anthropic documentation names as the most common. A self-verification instruction within the same turn addressed to Opus 5 is not, because it duplicates a behaviour the model already has.

## Family G — on iterative retrieval

Provisional sentences generated as a plan work better, as queries, than document titles (p. 26).

## Family H — order and roles

In pairwise comparison the order of the inputs also matters, and it heavily influences the judgment (p. 28). And the role serves the register, little the judgment: in the thirty works surveyed it appears in four or five cases, while the definition of the criterion is almost everywhere (p. 71).

## Family I — three operational details

If the output contains reasoning before the answer, the regex should look for the **last** occurrence of the label, not the first (p. 19). An extractor that recovers malformed outputs also recovers wrong answers: in the case study it raised accuracy and lowered F1 (p. 41). Labels matter: with `accept`/`reject` the model did not cooperate, with `entrapment`/`not entrapment` it started to answer (p. 72).

## Language of the prompt

The paper reports that English templates often perform better than those in the task's language, but also that human translations beat machine ones and that neither option always wins (pp. 20-21). It is measured on pre-2024 models and not confirmed by the Anthropic documentation. Practical rule: write the prompt in the language of the desired output, because the style of the prompt influences the style of the answer; use English when the prompt is technical and the output is code or data.

---

## What stays out, and why

- **Automatic prompt optimization** (AutoPrompt, APE, GrIPS, ProTeGi, RLPrompt, DP2O, pp. 16-18): they require a labelled dataset, a metric and an automatic loop. Mention it only in L3, and in that case with the paper's figure: DSPy beat the human prompt engineer on the test set, 0.548 against 0.53 F1, in sixteen iterations (p. 42). If the user has data and a metric, telling them pays off more than polishing the prompt by hand.
- **Exemplar selection from a corpus** (KNN, Vote-K, LENS, UDR, Prompt Mining, Memory-of-Thought, pp. 11, 13): they presuppose an example bank and retrieval at runtime.
- **Heavy ensembling** (DiVeRSe, COSP, USP, MoRE, DENSE, Max Mutual Information, Uncertainty-Routed CoT, Complexity-based, pp. 13-15): many calls, gains not demonstrated outside the benchmarks they were measured on.
- **Soft prompts, prompt tuning and outdated architectures** (pp. 64-65): they optimize weights and not text, or concern BERT's cloze prompts instead of the prefix prompts of all current models.
- **Specialist translation** (MAPS, Chain-of-Dictionary, DiPMT, DecoMT, p. 21): tasks out of scope. The image techniques from the same area of the paper (prompt modifiers, negative prompting, paired-image, image-as-text, p. 22) are not excluded: they are in `images.md`.
- **Benchmarks as predictions**: the numbers on gpt-3.5-turbo, GPT-4, PaLM and LLaMA2 remain order of magnitude and direction, never an estimate for Claude 5.

---

## Divergences between the paper and the Anthropic documentation

**Explicit reasoning.** The paper devotes a whole family to reasoning inducers and uses Zero-Shot-CoT as its reference technique (pp. 12-13). On Claude 5 thinking is on by default and adaptive. The documentation wins: inducers do not go in, and the family remains useful only when reasoning is needed as a visible artifact. The conflict is less sharp than it seems, because the paper itself finds Zero-Shot-CoT below the zero-shot baseline in its own benchmark (p. 33).

**Self-critique.** The paper reports improvements for Self-Refine, Chain-of-Verification and Self-Verification (pp. 15-16); the Opus 5 documentation says to remove verification instructions. It is not a compromise but a distinction: the documentation speaks of instructions within a single turn, the paper of chains across separate calls. Chains remain valid when the intermediate needs inspecting; internal instructions remain valid on Sonnet 5 if they carry the criteria, and do not go in on Opus 5.

**Structured output.** The paper records an unresolved conflict: structuring the output would reduce performance according to Tam et al. 2024, would improve it according to Kurt 2024, who disputes their method (pp. 5-6). The Anthropic documentation is clear: structured outputs exists as a feature and recent models respect complex schemas when asked. On evaluation prompts the two sources agree, because the paper also measures that JSON or XML improve the accuracy of the judgment (p. 27). The documentation wins.

**Role and persona.** The paper is lukewarm: role prompting improves open-ended outputs and "in some cases" accuracy on benchmarks (p. 12), and in the table of evaluators roles appear in few works out of thirty (p. 71). The documentation recommends a role in the system prompt, even of a single sentence. It is not a real contradiction: the role governs register and focus, not correctness. I keep it, with that rationale.

## Sources

Sander Schulhoff et al., *The Prompt Report: A Systematic Survey of Prompt Engineering Techniques*, arXiv:2406.06608v6, 26 February 2025. Covers the literature up to February 2024, 1,565 papers selected with a PRISMA process, text taxonomy in figure 2.2, p. 9.

Anthropic documentation, consulted on 9 September 2026:

- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices`
- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5`
- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5`
