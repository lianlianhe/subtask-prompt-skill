---
name: subtask-prompt-rewriter
description: Rewrite a one-shot prompt into a "delegate a sub-task thread + main thread babysits & verifies" structured prompt. Use for complex tasks needing iteration or a verified deliverable.
version: 1.0.0
---

# Subtask Prompt Rewriter

## Purpose
Transform a user's one-shot prompt into a three-stage structure: main thread writes a GOAL -> sub-thread pursues it -> main thread babysits and verifies until a verified deliverable is produced. Adds built-in steering and an extra layer of taste/quality verification.

## When to use
- Task is complex and hard to finish in one answer
- Needs trial-and-error or correctness verification
- Needs a plan-execute-verify loop instead of one-shot generation

## Core principles
1. Delegate: main thread writes a clear GOAL for a sub-thread
2. Persist: sub-thread actively pursues the goal until a verified deliverable
3. Babysit: main thread checks each step, writes a Babysitting note, drives redo if not passing
4. Adaptive model selection: match the model and reasoning mode to each sub-task's difficulty; do not default to the strongest model in its strongest mode for everything

## Steps
1. Extract the real intent and implicit acceptance criteria of the original prompt
2. Fill gaps: time anchor, scope, quantifiable definition of done
3. Rate each sub-task's difficulty (simple / medium / hard) and select the matching model tier and reasoning mode; escalate to a stronger model/mode only when verification fails
4. Apply templates/subtask_prompt_template.md to produce the structured prompt
5. Add acceptance checklist + counter-risks + separation of fact vs inference
6. Output a before/after comparison table

## Model selection by difficulty
When the target Agent supports choosing a model or reasoning effort, the main thread assigns each sub-task a tier instead of always using the strongest model in its strongest mode:
- Simple (lookup, formatting, boilerplate, short summaries): a fast/cheap model, minimal or no extended reasoning
- Medium (multi-step reasoning, moderate code, structured analysis): a mid-tier model with light reasoning
- Hard (deep reasoning, tricky debugging, novel design, high-stakes verification): the strongest model with high reasoning effort
Guidelines:
- Default to the lowest tier that can plausibly pass the acceptance criteria; scale up, do not scale down
- Escalate to a stronger model/mode only after a verification failure or when the babysitting note flags insufficient quality
- The babysitting/verification pass may itself run on a stronger model than the sub-task that produced the output
- Record the chosen tier and reasoning mode per sub-task so cost and quality trade-offs stay transparent

## Notes
- Keep a human final review for money, publishing, or irreversible actions
- Add a disclaimer for analysis tasks (not professional advice)
