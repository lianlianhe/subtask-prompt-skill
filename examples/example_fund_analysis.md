# Example: Rewriting a fund-holding analysis prompt

## Original (one-shot) prompt
> Based on my current fund holdings in the bank app, analyze the up/down trend of these funds over the next 6 months and give trading advice.

## Rewritten (subtask + babysit) prompt

You are the main thread, responsible for planning, supervision and verification. Do not give a final answer in one shot.

[Step 1 Delegate] Write a clear GOAL for another thread and require it to actively pursue the goal, keep overcoming obstacles (data gaps, inconsistent definitions), until a "verified deliverable" is produced.

[Step 2 Execute] Delegate the GOAL to the sub-thread.

[Step 3 Babysit & verify] After each output, check item by item and write a "Babysitting note": what verified / issues found / next correction. Drive redo until all acceptance items pass.

=== GOAL ===
Analyze the following fund holdings, judge the up/down trend for the NEXT 6 MONTHS (2026-07 -> 2026-12), and give trading advice for the current portfolio.

Holdings (analyze grouped by theme):
- Semiconductor / tech growth: 023829, 023828, 008888, 022364, 016874
- AI / robotics: 005844, 025505, 020482
- Broad-based / quality: 008528, 022454
- Fixed income / defensive: 001045 (convertible bonds), 015863 (interbank deposit / cash-like)
Note: 023829 & 023828 are A/C share classes of the same product; 008888 is a C class. Explain how A/C fee differences affect holding-period returns.

=== Verified deliverable requirements ===
1. Per fund: 6-month direction (up/down/range) + confidence + key reasons
2. Portfolio diagnosis: concentration, correlation, risk exposure
3. Trading advice: add / hold / reduce / exit + reason + take-profit/stop-loss reference ranges
4. Data sources and dates; separate fact vs inference
5. At least 3 counter-risk scenarios
6. Disclaimer: analysis only, not investment advice

=== Acceptance criteria (main thread checks each) ===
- Every conclusion has traceable data support, no unfounded claims
- A/C share classes, theme overlap, correlation risk are identified
- Fact vs inference clearly separated, sources and dates complete
- Trading advice gives executable ranges and triggers, not vague wording
- If any item fails, state it and have the sub-thread redo that part
