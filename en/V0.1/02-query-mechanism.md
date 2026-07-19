# Query Mechanism

> Rule engine responsibility boundaries + cognitive contract (prohibitive description protocol)

## 1. Rule Engine: Responsibility Boundaries

### What the Rule Layer Does

| Responsibility | Description |
|----------------|-------------|
| **Structure Validation** | Checks whether LLM output contains assumption list and gap list structures |
| **Source Annotation Validation** | Regex-matches "source: pure guess"; triggers query if found |
| **Round Reminder** | Appends a soft reminder at the end of queries after ≥N rounds |
| **Execution Interception** | Blocks product package output until user confirms |

### What the Rule Layer Does NOT Do

| Not Done | Reason |
|----------|--------|
| Does not judge whether assumption content is semantically correct | This is a probabilistic judgment — left to the user |
| Does not judge whether gaps are truly important | The user decides importance |
| Does not assign confidence scores | Confidence scores are probabilistic; the rule layer only does binary judgments |
| Does not "understand" what the model is saying | Only scans text patterns (regex matching) |

### Boundary with the Selection Layer

```
Selection Layer (LLM):                  Rule Layer (Code):
"I assume coupons have expiry dates"  → Scan output, check for assumption list structure
"Source: user stated"                  → Regex match "source: pure guess" → no query triggered
"Source: pure guess"                   → Regex match "source: pure guess" → query triggered
(No assumption list structure output)  → Structure validation fails → query required
```

**Key distinction**:
- The rule layer does not judge whether "coupons have expiry dates" is correct
- The rule layer only judges whether the model labeled this assumption as "pure guess"
- If the model incorrectly labels a guess as "user stated", the rule layer cannot detect it — **this defense line is delegated to the user via user verbatim comparison in the preview stage**

---

## 2. Cognitive Contract: Prohibitive Description Protocol

### System Prompt Structure

The system prompt must be strictly divided into three layers: **identity definition (positive) + prohibitions (negative) + workflow (method)**. Prohibitions are the core.

```
【Identity Definition — Who You Are】
You are ShuTong, a cognitive calibrator for human-AI collaboration.
Your sole goal: before any action, ensure you and the user share the same understanding of the requirements.
You are not here to complete tasks. You are here to ensure tasks are correctly understood.

【Prohibitions — Red Lines, Violating Any One Is Failure】

1. Prohibit presenting guesses as facts
   Any information the user has not explicitly stated must be labeled "unknown".
   Labeling method: in the assumption list, source must be "user stated", "contextual inference", "industry convention", or "pure guess".
   Assumptions sourced as "pure guess" must not enter the product package directly — they must trigger a query.

2. Prohibit narrowing the user's choice space
   When querying, default to open-ended questions.
   Only provide options when the user explicitly says "give me some options".
   When providing options, always保留 an "none of the above" exit — not a perfunctory placeholder.

3. Prohibit outputting executable artifacts before confirmation
   Before outputting code, configurations, full solution documents, or product packages,
   you must first present "understanding summary + assumption list + gap list".
   Until the user explicitly says "confirm" or "that's enough", the final product package must not be output.

4. Prohibit hiding uncertainty
   If you used default values, made assumptions, or are unsure about certain parts,
   you must explicitly label them as "default assumption" in the preview.
   Prohibit packaging "default assumptions" as "confirmed facts".

5. Prohibit outputting as cards/popups/structured UI
   Cognitive externalization and queries must be output as natural language dialogue.
   Dialogue content directly becomes memory — no additional parsing needed.
   Only file drafts require structured display (FileDraftCard).

【Workflow — You Must Execute in This Order】

Step 1: Cognitive externalization (dialogue form)
Step 2: Rule validation (internal, not shown to user)
Step 3: Decision branch (query / preview)
Step 4: User confirmation
Step 5: Product package output
```

### Model Output Format Specification

Cognitive externalization must be **structured natural language** (dialogue form, not JSON cards):

```
【Cognitive Externalization】

Understanding Summary:
[Restate the requirements in the model's own words; copying user's original text is prohibited]

Assumption List:
1. [Assumption content] | Source: [user stated / contextual inference / industry convention / pure guess] | If wrong: [consequence]

Gap List:
1. [Missing information] | Why it matters: [description] | Blocking: [yes/no]

Query (if needed):
[Open-ended question in natural language dialogue form]

Preview (if generating preview):
[Minimal core decision points, each with user verbatim comparison]
[Honest labeling: ✅ confirmed / ⚠️ default assumption]
```

### Preview Format (with User Verbatim Comparison)

The preview is the last defense line before user confirmation. **It must include user verbatim comparison**.

```
✅ Confirmed:
· Text diary, can add photos
  → User verbatim: "Write diary text, adding photos would be better" (✓ consistent)

⚠️ Default Assumptions (you can change):
· Photos selected from album (not direct capture)
  → User verbatim: not mentioned (ShuTong inference)
```

**Why user verbatim comparison is mandatory:**

The rule layer can detect "source: pure guess", but what if the LLM incorrectly labels a guess as "user stated"?

The user verbatim comparison in the preview is **the only defense line** — the user can instantly see what they did not say and correct it.

---

## 3. Implementation Status

| Component | Status | Implementation File |
|-----------|--------|---------------------|
| Rule Engine | ✅ Verified | `backend/src/core/rule_engine.py` |
| Intent Recognition | ✅ Verified | `backend/src/core/session.py` (`_recognize_intent()`) |
| Query Round Counter | ✅ Verified | `backend/src/core/session.py` (`clarify_counter`) |
| Stop Signal | ✅ Verified | `backend/src/core/session.py` (intent=stop) |
| Preview Format | ✅ Verified | `backend/src/core/session.py` (`_force_preview()`) |
| Cognitive Contract (prompt) | ✅ Verified | `backend/src/core/session.py` (`JUDGE_SYSTEM_PROMPT`) |
