---
name: writing-prompts
description: Writes and revises prompts to paste into another AI session - Claude, Claude Code, ChatGPT, Codex, Gemini or an image generator - and delivers the prompt without carrying out the task it describes. Open it before writing or rewriting any prompt, even a one-liner, whatever the wording (write a prompt, write me a prompt, draft a prompt for ChatGPT, give me, make me, turn this into a prompt, optimize, fix, improve), even when the request looks easy, already describes the whole task or asks for no questions, and whatever the prompt must then produce - research, an analysis, code, an email, a document, a procedure, material for a meeting - the job is the prompt, not what the prompt asks for. It also applies when the word prompt does not appear - how do I ask it, what do I tell Claude, I need Claude to write an email. Do not write the prompt on your own without opening it. If the user asks for the email or final text rather than a prompt, do not open it; if they ask for a skill, use skill-creator.
license: MIT. LICENSE.txt has complete terms
metadata:
  author: https://github.com/Apsion9231
---

# Writing prompts

<gate>
This skill delivers a prompt and does not carry out the task the prompt describes. If the request is "do X", the job is X and this skill is not needed; if it is "write me the prompt to do X", the job is the prompt and it stops there.

In practice: do not do the research, do not apply the fix, do not produce the analysis the prompt would ask for, and do not offer to do it at the end. Exploring the project or the web to anchor the prompt to verified facts is part of the job, and must be done.

The reason, which also covers cases not foreseen here: the prompt is for **another** session, and the user opens a clean chat or runs `/clear` as soon as they have it. Everything you carry out now is thrown away with the session, and in the meantime it has cluttered the context in which you are writing the prompt. Carrying out the task is not an extra: it is work wasted twice.

**"Just give me the prompt", "only write me a prompt", "I want the prompt ready to paste", even in all caps, are instructions about this gate: they say not to carry out the task.** They say nothing about the interview and do not authorize skipping it.
</gate>

Two sources with different roles. **The Prompt Report** (Schulhoff et al., arXiv:2406.06608v6, literature up to February 2024) gives the taxonomy: which techniques exist, what they cost, what evidence they have; the `p. N` references point to its pages. The **Anthropic documentation** (consulted on 9 September 2026) gives the behaviour of current models. Where they contradict each other, the documentation is right about how Opus 5 or Sonnet 5 behaves and the paper about what a technique does and what it costs; the concrete divergences are in `techniques.md`. The paper's benchmarks are measured on gpt-3.5-turbo and GPT-4, and the paper itself warns that techniques may not transfer to other models (p. 45): they show the shape of the phenomenon, never a prediction about Claude 5.

Precisely because the paper surveys techniques measured on models that are not Claude, the taxonomy, the parsimony criterion and the interview apply to any recipient. Only the calibration of Step 4 changes: on Claude it is the one in `models.md`, optimized in detail; on other AIs and image generators it is a deliberately thinner layer, in `other-ais.md` and `images.md`.

Every number and every `p. N` in this skill comes from the paper or the documentation: they are content to be read, not remembered. If you are about to cite one and have not opened the file that contains it, do not cite it.

## Boundaries

- Writing texts, articles, emails: the deliverable is not a prompt, and this skill is not needed.
- Writing or revising other skills: `skill-creator`.
- Models other than Opus 5 and Sonnet 5 (Fable, Mythos, Haiku, the 4.x family): technique selection still applies, the Step 4 deltas do not. Say so in one line and proceed without applying them by analogy.

---

## Step 1 — Classify and declare

Four classifications, almost always deducible from what the user has already written. They determine which calibration to apply (Step 4) and how many techniques to admit (Step 3); how many questions to ask is decided by Step 2.

**Recipient**, that is, where the prompt will be pasted. It decides which file you open at Step 4. Apply the first true row, in this order:

| If | Branch | Step 4 file |
|---|---|---|
| The prompt must generate or edit an image | Images | `images.md` |
| The request, the memory or the files name Claude, Opus, Sonnet, Haiku, Claude Code, claude.ai or the Anthropic API | Claude | `models.md` |
| They name another model or product (ChatGPT, Codex, Gemini, Copilot, Perplexity…) or say "another AI" | Other AIs | `other-ais.md` |
| None of the three | Not deducible | It is question D1 of slot 7, and becomes the first question |

Several recipients at once: one shared base prompt plus one calibration block per branch, not two whole prompts. The surface table below applies to the Claude branch; the surfaces of other AIs are in their own file.

**New or revision.** On a revision the starting point is the existing prompt: remove anti-patterns, add only the techniques that correct an observed failure, declare what changed. Rewriting from scratch a prompt that works moves the risk for no reason.

**Surface**, which changes the available levers, not just the style.

| Surface | What changes |
|---|---|
| Chat on claude.ai | Text levers only. No effort, thinking config or sampling parameters: mentioning them is noise. The separation between instructions and input is done with XML tags inside a single block. |
| Claude Code | Operational prompt with tools, files and actions. Scope, temporary files, destructive actions, parallel calls and subagents matter. |
| Reusable system prompt | Instantiated on different inputs: it needs explicit placeholders, a constrained format and, if code reads the output, answer engineering (family I). |
| API calls | Here `effort`, `thinking`, `max_tokens` and structured outputs exist. The parameter block goes in only here. |

When the surface is Claude Code, a reusable system prompt or an API call, open `surfaces.md` and take the blocks for that surface from there before writing the prompt. For chat on claude.ai there is no need to open it.

**Real complexity**, that is, how many independent decisions the model must make, not how long the request is.

- **L1, single use.** One request, one output, no rigid format, no reuse. Few techniques.
- **L2, repeatable.** Template reused on different inputs, system prompt, prompt for Claude Code with constraints, prompt someone else will use.
- **L3, pipeline.** Several chained calls, output consumed by code, a success metric. The prompt alone is not the deliverable: an extractor and an evaluation criterion are also needed, which the paper formalizes as joint optimization of the prompt-plus-extractor pair (p. 76).

**Declare the classification in one line, before working**: «revision, recipient Claude Code, L2, 7 empty slots out of 10, I'll ask you eight questions because the case data are two variables». Its purpose is to give the user the override point before the prompt exists, not to make noise. The number declared is the number of questions, not the number of blocks the surface's tool splits them into: eight questions remain eight even when they arrive in three blocks of three, three and two.

---

## Step 2 — Interview

**Ask.** The interview is the default, not the exception: missing information is asked for instead of assumed, because a question costs ten seconds and a false assumption costs the downstream run.

**The slots.** Before writing the prompt these ten must be filled, and you must know who filled each one.

1. **Form of the artifact** — table in chat, file, CSV, continuous prose, one card per item, code block. It is the physical form of what arrives, separate from what it contains: it is the slot assumed in silence most often of all.
2. **Perimeter** — what is in, what is out, and what to do with borderline cases. It is the perimeter of the **task**, not of the case: it says which piece of work is done, not what state the world being worked on is in, which is slot 10.
3. **Domain definitions** — specialist, internal or contested terms, and the criteria the user has expressed in their own words: "acceptable effort", "good listing", "reliable source", "non-impacting change". The paper's case study measures the cost of getting this wrong: asked about the construct to be labelled, the model gave a definition different from the human coders', and from then on the definition was included in every prompt (p. 35).
4. **Method and tools the user already has** — which sources to start from and in what order, with what verification standard, and which procedures already in use the prompt must follow instead of rewriting them from scratch. On a research task the method decides the result as much as the perimeter.
5. **Thresholds and numbers** — percentages, time cutoffs, minimum quantities, scores: every value that decides what survives a filter and what is discarded.
6. **Fields and content** — what each row, section or card carries, and what makes an answer acceptable rather than merely plausible.
7. **Recipient, model and surface** — up to two questions with the fixed options written here, because the value is a fact of the user's environment and not a decision to propose: no proposal first, no «You choose», and the free-text field where the surface has one. In Claude Code it is the free option of `AskUserQuestion`; in chat it does not exist, the options are buttons and anyone with a different value types it instead of pressing one — saying so in the line that introduces the block is enough, and it is not a reason to give up the tool. The two questions route everything else, so they jump ahead of the list order and open the block.
   - **D1, recipient**, if Step 1 does not deduce it: «Claude, chat» · «Claude Code» · «Another AI».
   - **D2, model or product**, if the request, the memory or the files do not name it. Claude branch: «Opus» · «Sonnet» · «I don't know», which means base prompt plus the two delta blocks of `models.md`. Other AIs: «ChatGPT» · «Gemini» · «Codex»; ask for the product, not the version. It follows D1 immediately, or opens the block if D1 is not needed.

   With questions forbidden, assume Claude, not a model: base prompt plus the two delta blocks, and in the receipt «recipient Claude, model not stated».
8. **Consumer of the output** — a person, a parser, another model, the user themselves three months from now. It changes the level of answer engineering.
9. **Empty result and false premise** — what the downstream model must do if the result is empty, if a constraint zeroes out the results, if it finds a contradiction with what the user told it.
10. **Case data** — the domain variables that describe the situation the prompt works on, not the form of the answer. Fill it like this: **name the variables that, if they were different, would change the structure of the answer — not those that would change one word of it**. In analytical chemistry, the matrix the analyte is in, which decides interferences, retention and wavelength; in a legal case, the type of proceeding; in a structural calculation, the applicable standard; in a data analysis, the size and origin of the dataset. Those the user has not written are questions, one per variable: there may be one, two or three. If no variable passes the structure filter, the slot is full and generates no questions.

**One variable, one question: if it is already the subject of another slot, it is asked there.** The slot 10 predicate also catches things that are already a threshold (slot 5), a perimeter (slot 2) or a definition (slot 3). In that case the question is asked only once, under the slot it belongs to, and counts as one. **When it is ambiguous, the other nine win**, because they have narrower predicates and downstream rules that work only if the value is there — the Step 5 number sieve takes a threshold only from slot 5. Slot 10 keeps what no other slot holds: facts that exist before the prompt and are ascertained rather than decided.

**The rule: every slot you filled instead of the user is a question, not an assumption**, even when the value is reasonable and even when the prompt is short. A slot is full only if the value is in one of these four forms: the user's words, a project file, the memory or the profile, the answer to a question you have already asked. In every other case it is empty, and empty means a question. **Being able to choose a sensible value does not fill a slot**: it is the condition in which the question is needed, not the one in which it can be skipped.

**A slot can be half full, and the missing half is a question.** It happens when the user names the thing but not its parameter: "a PDF" without the length, "a report" without the number of sections, "the best suppliers" without the measure of "best", "recent sources" without the cutoff date. The check is mechanical: if to write the prompt you had to add a number, a date, a unit of measure or the definition of an adjective the user did not give, that part of the slot was empty. Ask it.

**Do not attribute a decision of yours to the user.** A definition, a threshold or a criterion is theirs only if it appears in their words. Writing "the criterion you wrote" over a criterion you formulated is the hardest thing for the reader to correct, because it arrives already confirmed: it looks like an established fact and nobody questions it again.

**The list order is the question order, and it is not rearranged.** Empty slots are all asked, in the order they appear above. The order is fixed because it was derived from where prompts go wrong most: the form of the artifact comes first because it decides answer space, ordering and the tail of the output.

**Before asking, look where the answer is already written.** The user's memory, profile and preferences, CLAUDE.md, project files, earlier prompts of the same family, and **the other installed skills** (in Claude Code: `~/.claude/skills`, plugin skills, project files; on claude.ai: the account's skills and the files of the open project). A slot filled from there is not a question: it is a fact, and it is cited in the delivery with its source. That is what keeps the number of questions low honestly.

**For slot 4 this counts double.** Those sources often already contain the method: a hierarchy of sources, a list of portals, a card format, a verification policy. If it exists, the prompt **cites** it by name and declares only the points where it departs from it, instead of rewriting it or replacing it with an invented procedure. If it does not exist, the method is a question like the others.

**Always ask, except in three cases.** The user has explicitly forbidden questions: "don't ask me questions", "no questions", "go ahead with your assumptions". Only phrases like these count, and they stand as the answer to every slot, with the Step 5 receipt becoming mandatory. Or: all ten slots are filled by the request, the memory or the files. Or: it is a revision and the existing prompt answers the slots that matter by itself. In no other case is the interview skipped.

**These instead are not a ban on asking:** "just give me the prompt", "only write me a prompt", "I only want the prompt", "ready to copy and paste", a request written in capitals. They describe the deliverable and forbid carrying out the task — they are the gate at the start, not Step 2. Reading them as "doesn't want questions" is the error that turns a precise request into a skipped interview.

**How many questions: one for each empty slot.** First fill what you can from the request, memory, files and installed skills; then count the slots still empty. That number is the number of questions, with no ceiling and no minimum: one empty slot, one question; seven empty slots, seven questions. It is not lowered by the brevity of the request nor by how trivial the prompt seems to you. **Only one slot generates more than one: slot 10, which is worth one question for each domain variable not written.** When that happens, the questions outnumber the empty slots and the Step 1 declaration line carries both numbers. The other slot that can be worth two questions is 7, when both the recipient and the model or product are missing, as its line says. The other eight do not: one slot, one question.

**Form.** Multiple-choice questions, **always with the surface's interactive question tool**. Every Claude surface has one, chat included: before falling back on any other form, look in the session's tool list for the one that presents option questions to the user.

| Surface | Tool | Questions per block | Options per question |
|---|---|---|---|
| Claude Code | `AskUserQuestion` | 4 | 4, plus free-text field |
| Chat on claude.ai, desktop and mobile apps | the chat's question tool (`ask_user_input` or whatever it is called in the session) | **3** | 2-4, no free-text field |

Questions go in blocks in list order, filling each block up to the surface's ceiling: seven empty slots are 4+3 in Claude Code and 3+3+1 in chat. **The block ends the turn**: once the block is emitted, write nothing else and do not get ahead with the work, because the user's answer arrives as the next message and not as a tool result.

A numbered list in a message, with the options under each question, is the last resort: it is used only if the tool is truly missing from the tool list, or if the surface is not Claude. It is not a style choice, it is not the form to prefer when there are many questions, and the number of questions is never a reason to fall back on it: blocks chain. Open questions in sequence cost more friction and produce poorer answers than closed options.

The slot 7 questions have their options written there. Every other question is built like this:

1. **First option: your proposal.** It is the place where the value you would have decided in silence becomes visible and correctable, and often it is also the idea the user had not thought of. **The label is short, two to four words** — «24 months», «card per supplier», «primary sources only» — because it is the text of a button: a long label does not fit, and a label that does not fit is why a question block slides into prose. The fact that the first option is your proposal, and its reason, go in the line that introduces the block, not inside the option: «in each question the first option is my proposal — the 24-month cutoff because beyond that prices are no longer comparable».
2. One or two real alternatives, not cosmetic variants of the first.
3. **Last option, always: «You choose».** Whoever does not want to decide that slot delegates with one click, and the decision passes to the model.

Proposing a value is fine and useful; deciding it alone is not. The whole difference lies in whether the user sees it before it ends up in the prompt.

**If while exploring you find a contradiction, that is a question now.** When you read the code, the files or the web to anchor the prompt and discover that a fact the user took as true does not hold — a defect that is a documented decision, a file that does not exist, a constraint that would zero out the results — do not write the prompt on top of the contradiction and do not push it downstream. It is the moment of maximum value for a question, because it costs one line and changes the work.

This question goes through **even when the user has forbidden questions**: only one, and it must say what changes in the prompt if the answer differs from what they believed. The alternative is handing them a prompt built on a false premise, which is worse than an unwelcome question.

**Deferring a question into the prompt** (that is, instructing the downstream session to ask) is legitimate only when answering needs evidence that session has and you do not. If the matter can be closed now with one line, close it now.

---

## Step 3 — Technique selection

### Parsimony criterion

**Rule.** Start from the simplest technique that supports the task and add a technique only when you can say which failure mode it corrects. Every added technique carries a line of rationale in the delivery: if the line does not come to you, the technique does not go in.

**Baseline, valid almost always:** direct instruction, context on the goals, declared output format. It costs few tokens and solves most real failures, which are failures of specification and not of reasoning.

**Order of entry**, at increasing cost. Climb a step only after exhausting the previous one.

1. Elements that do not add calls and reduce ambiguity: definition of terms, success criteria, scope, format and answer space.
2. Exemplars, when format, tone or the boundary of a judgment are easier to show than to describe.
3. Explicit structure of the work (plan, sub-problems, table), when the steps are individually verifiable or the plan serves as an artifact.
4. Chaining across separate calls, when an intermediate result needs to be inspected or filtered.
5. Repetition and voting, only with a declared metric and budget.

**Three families from the paper are already internal to Claude 5 and must not be reproduced in the prompt.** Thinking is on by default and adaptive, so reasoning inducers add tokens without adding reasoning; the two exceptions are in `models.md`. On Opus 5, self-critique within the turn is already the model's behaviour; on Sonnet 5 a verification instruction remains useful if it carries the criteria. Decomposition and delegation are handled by the model in agentic contexts. They remain useful when you need the reasoning **as an inspectable artifact**: that is a different reason from improving performance, and it must be stated as such.

### Who owns the definition

`Operational definition` and `Scope` say what to put in the prompt, not where it comes from: it comes from whoever asked you for the prompt. If you write it yourself — a threshold, a taxonomy with its discard policy, the operational meaning of one of their criteria — it is slot 3 or 5 left empty, not a technique. Go back and ask.

### Operational taxonomy

Tables A-I apply to every text recipient, Claude or other AIs. On the image branch they are not used: in their place are the paper's image techniques, in `images.md`.

`techniques.md` holds the numbers that support them, the design decisions about exemplars, the techniques left out with the reason, and the divergences between paper and documentation: open it when you need the quantitative rationale, when the task is multi-stage, or when the rationale line does not come to you.

**A. Instruction, context, constraints** — step 1, often sufficient on its own.

| Element | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Directive | States the intent explicitly instead of implicitly | Always | — | 5 |
| Operational definition | Puts the definition of the key term in the prompt instead of taking it as known | Specialist, internal or contested term | A few lines | 35, 71 |
| Context on the goals | Says why the output is needed and how it will be used | Almost always | A few lines | 39 |
| Output format | Form, fields, length, and an example if needed | The output has an expected form | — | 5, 18 |
| Role | One system-prompt line that sets expertise and register | Open tasks, tone control | — | 12 |
| Style instructions | Declared style, tone, genre | Register matters | — | 12 |
| Scope | Says what is inside and outside the task | Narrow tasks, especially on Opus 5 | — | doc |

The figure that supports this family: removing from the prompt the email that explained the project's goals made F1 collapse by 0.27 points, from 0.45 to 0.18, and recall by 0.75 (p. 39). Explaining what the work is for paid off more than exemplars.

**B. Exemplars** — step 2. Three to five, inside `<example>` gathered in `<examples>`.

| Technique | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Few-Shot | Shows the task solved | Rigid format, judgment with subjective boundaries, tone to imitate | Tokens | 10 |
| Contrastive exemplars | Adds badly solved cases with why they are wrong | There is a recurring, recognizable error | Doubles the examples | 13, 38 |
| Ambiguous exemplars | Includes borderline cases with a debatable label | Classification where the boundary is the problem | Tokens | 32 |
| Balanced exemplars | Evens out the represented classes | Classification: the distribution of examples shifts the output | — | 32 |
| Generated exemplars (SG-ICL) | Have the model produce the examples and correct them | You have no real cases at hand | 1 call | 11 |

If you put in examples, the six design decisions (order, quantity, distribution, quality, format, similarity) are in `techniques.md`: order alone can move accuracy from below 50% to over 90%.

**C. Input handling** — when the problem is the request, not the task.

| Technique | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Rephrase and Respond | Has the request rephrased and expanded before executing it | The end user writes elliptical requests | 0 or 1 call | 12 |
| System 2 Attention | Rewrites the input removing what is irrelevant, then works on the cleaned version | Pasted input full of noise | 1 call | 12 |
| Repetition of the key constraint | Repeats the question or constraint at the end of the context | Long context, constraint easy to lose | Tokens | 12, 40 |
| Question Clarification | The model asks before answering if the request is ambiguous | Interactive prompt meant for other users | One turn | 32 |
| Quote extraction | First the relevant quotes in `<quotes>`, then the task | Long documents | Tokens | doc |

**D. Structure of the work** — goes in to make the process inspectable, not to make the model reason better.

| Technique | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Step-Back | First a question of principle, then the task | You want the chosen criterion to be visible and debatable | — | 13 |
| Plan-and-Solve | Explicit plan, then execution of the plan | The plan serves as an artifact to approve | — | 14 |
| Least-to-Most | Lists the sub-problems, then solves them in sequence, accumulating | Sub-results are verifiable one by one | n calls | 13 |
| DECOMP | Decomposes and delegates to separate functions or tools | Dedicated tools exist for the sub-tasks | n calls | 14 |
| Tabular reasoning | Emits the reasoning as a table | Comparison on fixed, repeated dimensions | — | 13 |
| Program-of-Thoughts / PAL | The reasoning is code that gets executed | Calculation, counts, aggregations over data | Execution | 14, 24 |
| Skeleton-of-Thought | Skeleton of the answer and parallel expansion | The goal is latency | Parallel calls | 14 |
| Tree-of-Thought | Tree of alternatives with evaluation and backtracking | Real search with constraints; on Claude 5 the agent usually does it already | Many calls | 14 |

**E. Repetition and voting** — only in pipelines, only with a metric.

| Technique | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Self-Consistency | k answers at non-zero temperature, then majority | Single, comparable answer, measured variance, declared budget | k calls | 15 |
| Universal Self-Consistency | The aggregation is done by a model, not a counter | Free-text output, not comparable for equality | k+1 | 15 |
| Meta-CoT | Generates several chains and synthesizes the answer from all of them | As above | k+1 | 15 |
| Prompt Paraphrasing | Variants of the same prompt | You want to measure how much the result depends on the wording | k | 15 |

On Sonnet 5 `temperature` returns an error: variety is obtained by varying the prompt, not the sampling.

**F. Critique and revision** — chained across separate calls when the intermediate needs to be read or filtered; within the turn only with explicit criteria, and never on Opus 5.

| Technique | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Self-Refine | Draft, critique, revision, in distinct calls | You want to read, archive or filter the critique | 2n calls | 15 |
| Chain-of-Verification | Generates verification questions on the asserted facts and answers them before revising | Factual output at risk of hallucination, with no verification tools | 3+ calls | 16 |
| Self-Calibration | A second call that assesses whether the answer holds | An explicit threshold is needed to accept or reject | 1 call | 15 |
| CRITIC | Answer, self-critique, verification with external tools | There are tools that can really verify | 3 phases | 24 |

A chain across separate calls is legitimate, because it serves to inspect or filter an intermediate. A self-verification instruction within the same turn addressed to Opus 5 is not, because it duplicates a behaviour the model already has. On Sonnet 5 it goes in if it carries the criteria with it («check the result against the edge cases listed above»); a generic «double-check» goes in on no model.

**G. Agents and retrieval** — relevant on Claude Code and on pipelines with tools.

| Technique | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| ReAct | Thought, action, observation loop, with everything kept in memory in the prompt | Agent with tools in an environment | — | 25 |
| Reflexion | Adds to memory a reflection on the evaluated failure | The task can be retried and the failure is measurable | — | 25 |
| RAG | Retrieves external information and inserts it into the prompt | The knowledge lies outside the model | Retrieval | 25 |
| IRCoT | Alternates retrieval and reasoning, each guiding the other | Multi-hop questions | n calls | 26 |
| FLARE | Generates a provisional sentence and uses it as a search query | Long, factual generation | Repeated retrieval | 26 |

**H. Evaluation, that is, Claude as judge** — the family with the most directly applicable evidence.

| Element | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Definition of the criteria | Puts in the prompt the definition of what is being evaluated | Always: it is the most frequent element in the thirty works surveyed | — | 71 |
| Model-generated rubric | Have the criteria generated, freeze them, then evaluate with them | Criteria are poorly defined and evaluations come out inconsistent | 1 call | 27 |
| JSON or XML output | Structures the judgment | Always: structure improves the accuracy of the judgment | — | 27 |
| Likert scale with verbal labels | Gives meaning to the levels instead of bare numbers | Subjective scores | — | 27 |
| Individual scoring instead of pairwise | Evaluates each text on its own | Comparisons between texts | — | 28 |
| One instance per call | Avoids batching several cases into one prompt | When quality matters more than cost | Higher cost | 27 |
| Different roles or debate between roles | Diversifies the judgment | Variety of perspectives is needed, not a single verdict | k calls | 26 |

**I. Answer engineering** — when the output is read by code (pp. 18-19, 76).

| Element | What it does | Goes in when | Cost | p. |
|---|---|---|---|---|
| Answer shape | Fixes the physical form: single token, line, object | Classification, extraction | — | 18 |
| Answer space | Lists the allowed values | Closed labels | — | 18 |
| Verbalizer | Maps the output onto the internal label | The system's labels are not the natural ones for the model | — | 18 |
| Extractor | Regex or a second call that isolates the answer | Pipeline with a parser | — | 19 |
| Label names | Chooses labels the model agrees to produce | The model refuses, hesitates or answers "cannot be determined" | — | 72 |

### Recognition pass

Before delivering, look at what you built by hand and check whether it already has a name in these tables. "Ten numbered questions to solve in sequence" is `Least-to-Most` (p. 13). "Check the result against an explicit criterion, then revise" is `Chain-of-Verification` (p. 16). "First the relevant quotes, then the task" is `Quote extraction`. "List the criteria, freeze them, then judge" is the generated rubric of family H.

If it has a name, call it by its name and with its page in the rationale line. That is what makes the rationale verifiable against the paper instead of being a paraphrase, and it forces you to choose from the whole table instead of from the seven rows of family A.

---

## Step 4 — Calibration and anti-patterns

Before delivering, open the file of the branch decided at Step 1 and apply deltas and anti-patterns: `models.md` for Claude, `other-ais.md` for other AIs, `images.md` for image generators. The Step 5 techniques line must name the delta you applied and the file it comes from: if you have not opened the file you cannot write it, and that is how a skipped reading shows up in the delivery instead of going unnoticed.

When you revise an existing prompt, or when you must show the user the effect of a removed anti-pattern, open `examples.md` and reuse the before/after pair for the surface in question.

### Rules for writing the prompt

They apply to text prompts. The two marked *Claude branch* come from the Anthropic documentation; on other AIs the structure is governed by `other-ais.md`, on images by `images.md`.

- Be explicit about format and constraints. If you want above-and-beyond behaviour, ask for it: it is not to be inferred from a vague prompt.
- Explain the why of every instruction. "Do not use ellipses because the text will be read by a TTS engine" works better than "do not use ellipses": the model generalizes from the motivation to cases you did not foresee, from a bare rule it does not.
- *Claude branch.* Use XML tags to separate instructions, context, examples and variable input, with consistent names across prompts.
- *Claude branch.* Context over 20k tokens: documents at the top, question at the bottom, each document in `<document>` with `<source>` and `<document_content>`. The question at the bottom improves quality by up to 30% in Anthropic's tests; the paper measures the same order of magnitude on the question format, which moves GPT-3's accuracy by up to 30 points (p. 31).
- To make the model act, use the direct imperative ("change this function") and not the consultative form ("can you suggest changes"), which produces suggestions instead of actions.
- Do not repeat in the prompt the preferences already active in the user's profile — language, tone, terminology level, preferred format — unless the prompt is meant for a context separate from their account, for example an API call or another person's session. If it ends up there, the prompt must carry them with it because they are not active there.

---

## Step 5 — Delivery

Before writing the delivery, check the finished prompt.

**Count the questions.** How many slots did the user not fill, and how many questions did you ask? If the questions are fewer, the difference is slots decided by you: either they become questions now, even with the prompt written, or they go into the receipt one by one, with the value you chose.

**Pass the prompt through the number sieve.** Reread the text and stop at every number, date, duration and maximum length: sixty characters for the subject line, three title variants, last twelve months, at least four items, seven characters of hash. For each one you must be able to say which answer it comes from. Those that do not come from an answer are your decisions even when they are standard practice: either they leave the prompt, or they go into the receipt with the value and the reason. **A small number is a threshold like any other**: it decides what is discarded.

**Closing gate: go through the ten slots, not the list of assumptions.** Take the Step 2 list from 1 to 10 and for each one, in order, say who filled it: the user, a file or the memory (with the source), or you. Every slot filled by you has either already become a question, or goes into the receipt. There is no third way, and obviousness does not open one. Rereading the assumptions is not enough: a slot that was filled and never written there is not there, and that is how surface and model, decided in silence, disappear from the delivery.

For every slot that ends up in the receipt ask yourself: *if this is false, does it change the structure of the prompt or one word?* If it changes the structure it was a question: go back to Step 2 and ask it, even with the prompt written. If you are about to write "if one of these is false the perimeter changes", you have already admitted it. **Even when it changes only one word**, a threshold or a definition you invented (slots 3 and 5) is a question: declare it without asking only if the user has forbidden questions.

**Slots delegated with «You choose» go into the receipt**, with the slot and the chosen value: «slot 5, delegated: threshold at 900-1200 words». The delegation is the user's, the choice is yours.

**The delivery, in this order:**

1. **The remaining assumptions**, ordered by impact, each on one line the user can answer yes or no to. If there are none, one line: «Assumptions: none». They come before the prompt so the user reads them before copying it: afterwards, they are a receipt and not a question.
2. **The prompt**, in a code block ready to copy, with no internal comments. Count the lines of all the blocks: beyond about forty the prompt is saved as a markdown file, and its path goes here.
3. **Recipient, model and surface**, with what the branch file says to tell the user outside the prompt: a control to set, a result to double-check.
4. **The techniques used**, one line each, with the table name and the page when they have one, and the failure they correct. A technique without its line leaves the prompt. The calibration delta also goes here, with the branch file it comes from.
5. **The slots that did not become questions**, grouped by who filled them: the user's words, or memory, profile or project files with the source. Every number from 1 to 10 appears either here, or among the questions, or in the assumptions: a number that appears in none of the three is a slot skipped in silence.
6. **On a revision**, what you removed and why.

This step applies to every prompt, even L1, even three lines long: the length of the request measures nothing.

---

## Red flags

These thoughts mean you are about to skip Step 2. They are the recurring phrases observed while testing the skill, not hypotheses.

| Thought | Reality |
|---|---|
| "The request was already decidable", "I know how to fill this slot" | Being able to decide does not mean the decision is yours. Full means filled by the user, a file, the memory or an answer. |
| "No interview: the request was already complete" | Complete on the task, not on the criteria: this sentence precedes a structural assumption. |
| "A wrong assumption gets corrected in a second round" | The second round is a deep research run or a session on a real repo. The question costs ten seconds. |
| "I'll declare them at the end, so whoever reads can correct them" | They read them after copying the prompt. Declaring is not asking. |
| "The output form is obvious, a table and that's it" | It is slot 1, the first on the list: the one most often assumed in silence. |
| "I'll propose the threshold myself", "I'll write the operational definition" | Thresholds and definitions belong to the user. Proposing a value is done inside a question; written by you it is an assumption disguised as merit. |
| "We're there, I'll do the research too", "meanwhile I'll get started" | This skill delivers a prompt for another session. Carrying out the task is wasted work and cluttered context. |
| "I know the model delta by heart" | The `p. N` references and the numbers cannot be memorized, and the few lines that matter are in the branch file: `models.md`, `other-ais.md` or `images.md`. If you have not opened it, you cannot cite it. |
| "They said ONLY WRITE A PROMPT, so they don't want questions" | They said not to carry out the task. About the interview they said nothing: the questions are asked. |
| "They forbade questions, so I also keep quiet about the contradiction" | One question always goes through: the one about a premise the facts contradict. Only one, and it says what changes in the prompt. |
| "They forbade questions, so I also skip the receipt" | It is the case where the receipt matters most: assumptions at the top of the delivery, ordered by impact. |
| "I'll set up the research method myself, I know how it's done" | It is slot 4. First look at the skills and files of whoever launched the session: if the procedure already exists the prompt cites it, if it does not it is a question. |
| "It's a trivial prompt", "too many questions, I'll assume some myself" | No ceiling: the number of questions is the number of empty slots, not your judgment. Whoever does not want to answer has «You choose». |
| "They said PDF, the format slot is full" | They said the thing, not the parameter. The length, the number of sections, the measure of an adjective: if you added it, that half was empty. |
| "Sixty characters for the subject line is standard practice, not a decision" | In the prompt a small number cuts like a large one. It comes from an answer, or it is yours and you declare it. |
| "I know the domain, I'll deduce the case data" | Deducing is not ascertaining. Case data are the user's facts: if they would change the structure of the answer and they did not write them, they are slot 10. |
| "The criterion you wrote in the request" | Look at their words: if the criterion is not there, it is yours. Attributing it to them makes it unassailable for the reader, and it is a decision of yours disguised as a fact. |
| "There's no question tool here in chat", "too many questions for buttons, I'll make a list" | The chat tool exists: look at the tool list before saying it is missing. The ceiling is three questions per block, and blocks chain — the number of questions never decides the form. |
| "The option must explain why I'm proposing it" | The label is the text of a button: two to four words. The why goes in the line above the block. A long label is what makes the interview degrade into prose. |

---

*Version 1.54 — 3 October 2026.*
