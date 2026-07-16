# Example: Rewriting a Kubernetes troubleshooting prompt

## Original (one-shot) prompt
> Pods on my EKS cluster keep getting stuck in CrashLoopBackOff. Find out why and fix it.

## Rewritten (subtask + babysit) prompt

You are the main thread, responsible for planning, supervision and verification. Do not give a final answer in one shot.

[Step 1 Delegate] Write a clear GOAL for another thread and require it to actively pursue the goal, keep overcoming obstacles (missing logs, unclear scope), until a "verified deliverable" is produced.

[Step 2 Execute] Delegate the GOAL to the sub-thread.

[Step 3 Babysit & verify] After each output, check item by item and write a "Babysitting note": what verified / issues found / next correction. Drive redo until all acceptance items pass.

=== GOAL ===
Diagnose and resolve the CrashLoopBackOff state of pods in the target EKS cluster, and deliver a reproducible root-cause + fix.

Scope (investigate systematically):
- Pod/container: `kubectl describe pod`, previous-container logs (`kubectl logs --previous`), exit codes, OOMKilled status
- Config: liveness/readiness probe settings, env vars, ConfigMap/Secret mounts, image tag/pull errors
- Resources: CPU/memory requests & limits vs actual usage, node pressure/eviction
- Dependencies: init containers, service/DNS reachability, IAM/IRSA permissions for AWS API calls
- Cluster: node status, kubelet events, CNI (Calico) or networking issues

=== Verified deliverable requirements ===
1. Root cause: the single confirmed reason (not a guess), with the evidence that proves it
2. Fix: exact manifest/config change or command, plus why it addresses the root cause
3. Verification: how you confirmed the fix (pods Running, probes passing, no restarts over N minutes)
4. Evidence log: commands run and their key output; separate observed facts from inference
5. Rollback plan: how to revert safely if the fix regresses

=== Acceptance criteria (main thread checks each) ===
- Root cause is proven by evidence, not assumed
- Fix is concrete and executable, not vague advice
- Verification shows pods stable (Running, 0 restarts) after the change
- Facts vs inference clearly separated; commands and outputs recorded
- Rollback plan included
- If any item fails, state it and have the sub-thread redo that part
