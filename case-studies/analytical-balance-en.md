**English** · [Italiano](analytical-balance-it.md)

# Case study: the prompt for an analytical-balance verification SOP

A real claude.ai session, from request to delivery, reported in full. It shows how the skill works: what it reads, what it asks, what it decides and what it delivers.

| | |
|---|---|
| Date | 3 October 2026 |
| Surface | Chat on claude.ai, outside any project |
| Model | Claude Opus 5.5, Medium effort |
| Skill | `writing-prompts` 1.53, the English version. The session ran in Italian because the account's preferred language is Italian |

**This is a translation.** The session took place in Italian; everything below — request, questions, options and the delivered prompt — is translated from the [Italian transcript](analytical-balance-it.md). The screenshots show the original Italian interface.

**What was changed from the session.** Four points, anonymized and marked in square brackets: the company name in one option, the role and industry in the prompt's context, the same data in the «from your profile» line, and the titles of the memory entries. Everything else is reported without edits. The delivered prompt was **not run**: the skill delivers the prompt and stops there.

---

## 1. The request

> Write me a prompt for Claude to help me prepare a procedure for the periodic verification of an analytical balance in the laboratory

## 2. Before the questions: what it read

The summary of the visible reasoning in the chat, in order:

- Loaded skill — `writing-prompts`
- Sorting out how to approach the request.
- Read memory — *[two of the user's memory entries]*
- Scoping the audit checklist and required documentation.
- Listing balance specifications and reference weight requirements.
- Drafting the reusable L2 prompt structure with its question blocks

The skill opened on its own, read the user's memory before asking, and classified the task as L2 (repeatable prompt).

## 3. The interview: eleven questions in three blocks

The questions arrive in the chat's interactive pop-up. In every question except the first two, the first option is the skill's proposal and the last is «You decide». Each question also has a free-text field («Something else»).

![The chat pop-up: the third question of the first block (Italian interface)](img/question-block-1.png)

The chosen answer is in bold. The «Slot» column is a **curator's note**: it shows which of the skill's ten slots the question fills.

**Block 1 of 3**

| # | Question | Options | Slot |
|---|---|---|---|
| 1 | Where will you paste the prompt? | **Claude, chat** · Claude Code · Another AI | 7 |
| 2 | Which Claude model will you use? | **Opus** · Sonnet · Don't know | 7 |
| 3 | What form should the procedure take? | SOP + record form *(the procedure text plus the sheet/log where the checks are recorded)* · **SOP text only** · Form/checklist only · You decide | 1 |
| 4 | Which checks are part of the periodic verification? | Daily + periodic *(daily check before use (sensitivity) plus full periodic verification (repeatability, accuracy, eccentricity, linearity, minimum weight); external calibration excluded)* · **Full periodic only** · Include calibration management *(also the interface with the external ACCREDIA calibration and out-of-tolerance handling)* · You decide | 2 |

**Block 2 of 3**

| # | Question | Options | Slot |
|---|---|---|---|
| 5 | Which standard should the procedure be based on? | **USP &lt;41&gt; + &lt;1251&gt;** *(the repeatability/accuracy and minimum-weight criteria most used in pharma/nutraceuticals)* · Ph. Eur. 2.1.7 · EURAMET cg-18 / OIML R76 · You decide | 4 |
| 6 | Who sets the acceptance criteria and the frequency of the checks? | From the chosen standard *(Claude derives them from the standard and cites them, without inventing any)* · I provide them *(the prompt will contain placeholders to fill in)* · Manufacturer tolerances · **You decide** | 5 |
| 7 | What structure should the SOP have? | [Company] SOP structure *(purpose, scope, responsibilities, references, definitions, materials, procedure, criteria, actions on nonconformity, records)* · **Free structure** · I'll paste it · You decide | 6 |
| 8 | Who will read the SOP? | **Technicians + QA** *(executable at the bench and defensible in an ISO 13485 audit)* · QA/audit only · My own internal use only · You decide | 8 |

![The chat pop-up: the first question of the second block (Italian interface)](img/question-block-2.png)

**Block 3 of 3**

| # | Question | Options | Slot |
|---|---|---|---|
| 9 | If Claude is missing a datum or finds a conflict with the standard, what should it do? | **Ask before writing** *(list the questions, then draft)* · Placeholder [TO BE DEFINED] *(write anyway and mark the open points)* · Assume and declare · You decide | 9 |
| 10 | What balance is it (readability d)? | **Analytical d=0.1 mg** · Semi-micro d=0.01 mg · Several different balances · You decide *(generic prompt with instrument data as placeholders)* | 10 |
| 11 | Which reference weights do you have for the checks? | **Class E2, certified** · Class F1, certified · Don't know / to be defined · You decide | 10 |

After the answers, the visible reasoning shows: *Read memory, read a file — Read `models.md` — Defining the criteria and the frequency for the delegated slot.* It is the file with the Opus deltas, which the skill opens before delivering a prompt for Claude.

## 4. The delivery

Reported in full, in the order it arrived.

**Assumptions (answer yes/no):**

- The "full periodic" verification covers repeatability, accuracy, eccentricity, linearity and minimum-weight verification. It excludes the daily check and the management of the external calibration.
- Acceptance thresholds and frequencies are not written in the prompt. Claude derives them from USP ⟨41⟩/⟨1251⟩, citing the section, and proposes the frequency on a risk basis. That is slot 5, which you delegated to me.

**The prompt** (translated):

```
<context>
I work [in an analytical development laboratory] operating under ISO 13485, not GMP. I need to write an SOP for the internal periodic verification of an analytical balance. The verification sits between one external calibration and the next, which stays outside this document. The SOP will be used at the bench by technicians and must withstand an ISO 13485 audit in front of QA: it must be executable without interpretation, and every criterion must be traceable to its source.
</context>

<case_data>
- Instrument: analytical balance, readability d = 0.1 mg
- Available reference weights: OIML class E2, with certificate
- Standard: USP <41> Balances and USP <1251> Weighing on an Analytical Balance
</case_data>

<task>
Write the text of the SOP for the full periodic verification of the balance. The verification covers repeatability, accuracy, eccentricity, linearity and determination or confirmation of the minimum weight. The daily check before use and the management of the external calibration are out of scope: if they are needed as context, mention them in one line.

Acceptance criteria and frequency:
- Derive acceptance criteria, test weights and number of weighings from USP <41>, and the frequency of the checks from USP <1251>.
- For each criterion, state the chapter and section it comes from.
- Where the standard leaves a choice to the laboratory (frequency, choice of weights, safety factor on the minimum weight), propose a justified value and mark it as «laboratory proposal». This way the reader can tell what is normative from what is our decision.
- If you are not sure of the wording of the current revision of a chapter, say so at that specific point instead of reporting a value as certain.

Structure: choose the sections yourself. But include the handling of a nonconforming result, that is, what the technician does if a test fails, and the data to record for each test.
</task>

<before_writing>
If you are missing necessary data, or if you find a conflict between what is written here and USP <41>/<1251>, stop before drafting. Examples of missing data: maximum capacity, nominal weights actually available, environmental conditions. List the questions and wait for the answers.
</before_writing>

<format>
Only the text of the SOP, in technical prose with numbered sections. Keep the length proportionate to an operating procedure: no filler introductory section and no theoretical explanation the technician does not use at the bench. Stay within the stated scope: if you think something important is missing, point it out in one sentence at the end instead of adding it to the document.
</format>
```

**Recipient:** Claude Opus, chat on claude.ai. Your preferences (Italian, terminological rigour, source checking) remain active in the account, so I did not repeat them. Still, check the numerical values of USP ⟨41⟩ against the current official text: Claude reports them from memory unless it runs a search.

**Techniques used:**

- Operational definition and Scope (table A). Verification is separated from calibration and the perimeter of the tests is explicit. It prevents Claude from silently including the daily check.
- Context on the goals (table A, p. 39). It explains that the document must be usable by technicians and defensible in an audit. From this, Claude derives the traceability of the criteria even in cases I did not foresee.
- Question Clarification (table C, p. 32). Claude stops before writing if data are missing. It is your answer to slot 9.
- Separation of normative values from proposals. It corrects the main risk: invented thresholds presented as if they were USP.
- Opus delta (`models.md`):
  - a scope constraint with «point it out in one sentence», because Opus tends to broaden the task;
  - concision asked for explicitly, because Opus produces longer documents;
  - no self-verification instruction, because Opus already does it and adding one only produces redundant checks.

**Slots filled without a question:**

- Slot 3 (what "periodic verification" means): decided by your choice of perimeter.
- ISO 13485 non-GMP context [and role]: from your profile.
- All the other slots: from your answers. Slots 5 and 6 (thresholds and structure) were delegated by you, and the choice I made is shown above.

---

## What this case shows

*Curator's note: facts observable in the session, with no judgment on the prompt.*

- **Triggering.** The request contains no keyword other than «prompt», and the skill opens on its own.
- **Sources before questions.** It reads the memory before asking: the ISO 13485 context is not asked for, because it is already in the profile.
- **One question per empty slot.** There are eleven questions: slot 7 is worth two (recipient and model), slot 10 two (balance and weights), slot 3 none.
- **Proposal first, delegation last.** In questions 3-11 the first option is the proposal and the last is «You decide». Questions 1 and 2 have fixed options and no «You decide», because recipient and model are facts, not decisions.
- **Delegation ends up in the receipt.** Slot 5, delegated («You decide»), appears among the assumptions with the choice made: thresholds derived from USP and cited, not written into the prompt.
- **The order of the delivery.** Assumptions, prompt, recipient, techniques with the paper's page, the slots and who filled them.
