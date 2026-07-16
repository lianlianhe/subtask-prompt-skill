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

## Steps
1. Extract the real intent and implicit acceptance criteria of the original prompt
2. Fill gaps: time anchor, scope, quantifiable definition of done
3. Apply templates/subtask_prompt_template.md to produce the structured prompt
4. Add acceptance checklist + counter-risks + separation of fact vs inference
5. Output a before/after comparison table

## Notes
- Keep a human final review for money, publishing, or irreversible actions
- Add a disclaimer for analysis tasks (not professional advice)
