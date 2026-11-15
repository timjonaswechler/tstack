---
name: council
description: Trigger this if the user says 'council'.
---

# Council

Stay within the current task's permissions. Council adds reviewers, not edit,
commit, push, PR-write, or testing-method permissions. For a read-only request,
report known issues without fixing them. Where fixes and checks are authorized,
address outstanding PR comments and known issues before the audit. If the user
wants to discard the implementation, skip fixes to code being replaced.
Explicit task restrictions and higher-priority harness rules always apply.

Bring in three independent delegates to help with the user's request, usually
for review or feedback. First read and follow
[OpenAI review models](../openai-review-models/SKILL.md) for model selection,
reviewer focuses, and delegation. Start the three reviewers in parallel using
that policy; the same OpenAI model may fill all three seats.

For each fresh review round in T3 Code, use a new `delegate_task` per reviewer
with the original brief, prior findings, responses, and unresolved objections.
Retain each `taskId` and use a distinct `clientRequestId` per reviewer and round.
The returned `childThreadId` is storage, not a target for further review rounds.

Give each delegate the request and relevant context. For review or feedback,
keep their work read-only and forbid further delegation. Collect their responses
and summarize the useful findings, disagreements, and recommended next steps.
Pin the goal, base, scoped paths, and working-tree contents (including untracked
files); all reviewers inspect the same state. Existing observed check results
may be reused only while they cover that unchanged state.

Do not wait idle. When a result arrives, check its findings against the code
at once and decide what to fix while the other delegates run. Do not edit
files while a delegate still reviews the same checkout. If the user's
instructions mark a delegate as non-blocking, do not wait for it; treat a
late result as feedback and do not record its score.

For a code review, ask each delegate for a score out of 10.
Count each full review pass as one round.
A fresh review after fixes starts a new round.

If the work ends in a pull request, put the results in the PR description.
Put this section before the model and harness line at the end:

```markdown
## Council review

Rounds: 2

| Delegate | Round 1 | Round 2 |
| --- | --- | --- |
| <model> — correctness | 7/10 | 9/10 |
| <model> — security/data integrity | 8/10 | 10/10 |
| <model> — tests/spec compliance | 7/10 | 9/10 |
```

If a delegate gives no score, write `no score`. Do not invent one.
If the PR is open and PR edits are authorized, edit the description. Do not post
a comment for this. Otherwise put the results in the final reply only.

## Budget and stopping

- Default: at most three rounds, with at most three reviewers per round.
  Retries and replacements consume reviewer slots; they do not reset the budget.
  An explicit lower time, cost, or round limit wins. Do not extend silently.
- In read-only mode, finish after the requested review and report findings.
  With fix permission, make only agreed scoped changes, rerun affected checks,
  and request a fresh review only while budget remains. Later rounds focus on
  changed code and open findings; optional nits do not require another round.
- Stop on agreement covering the current state, a blocker preventing review,
  or the first budget limit. Missing reviewers, errors, and timeouts are gaps,
  not approval. Report unresolved findings and disagreements at the limit.
- Scores describe reviewer opinions, not executable proof or shipping permission.
  Do not chase a score or let repeated reviews expand the task.
