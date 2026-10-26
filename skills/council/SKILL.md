---
name: council
description: Trigger this if the user says 'council'.
---

# Council

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
keep their work read-only. Collect their responses and summarize the useful
findings, disagreements, and recommended next steps.

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
If the PR is open, edit the description. Do not post a comment for this.
If there is no PR, put the same results in the final reply only.

If you agree with their recommendations, make the changes and request a fresh
review after. Continue this process until all delegates are satisfied with the
outcome.

Do not let repeated reviews result in scope creep. It is your job to fight to
keep scope limited.
