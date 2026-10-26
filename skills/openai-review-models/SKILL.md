---
name: openai-review-models
description: Use before choosing reviewer models for council or sol-loop.
---

# OpenAI review models

This fork uses OpenAI models only. Apply this policy when selecting reviewers
for `council` and `sol-loop`; the workflow names do not require GPT Sol or
multiple providers.

## Selection

1. Discover the models available through the current harness. In T3 Code, use
   `orchestrator_capabilities` (search the catalog if needed). Elsewhere, use
   the native subagent model catalog. Resolve actual model IDs at runtime.
2. Use explicitly requested OpenAI models when available. Otherwise reuse the
   parent's model if it is an available OpenAI model. If not, show the available
   OpenAI choices and ask the user to select one. Report an unavailable or
   non-OpenAI request instead of silently substituting.
3. For `sol-loop`, start one reviewer. For `council`, start three independent
   reviewers. Reusing the same OpenAI model and provider instance is expected;
   separate providers or distinct model IDs are not required. Give the council
   reviewers different focuses: correctness, security/data integrity, and
   tests/spec compliance. Each may still report any substantive issue.
4. Announce the selected model(s) and focuses before delegation. Multiple
   reviewers of one model are independent passes, not provider diversity.

## Delegation

In T3 Code, prefer native subagents only when they support the selected model
and no model options are requested or inherited. Otherwise use `delegate_task`
with the resolved provider instance and model. Pass requested or inherited
settings through `target.options` using the model's advertised option IDs;
report unsupported settings rather than approximating them. Verify the returned
provider, model, and options.

Outside T3 Code, use native subagents only if they can run the selected OpenAI
model with the required options. If no supported route exists, report the
blocker. A missing tool or model is not approval to use a different provider.
