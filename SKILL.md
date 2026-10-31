---
name: vibecode
description: >-
  Strictly enforces a Propose-Audit-Write multi-agent loop for writing or modifying code. Demands that any proposed code must first be sent to an adversarial subagent (using the 'flash' model) for auditing against relevant skills, domain invariants, security, and best practices before being written to the filesystem.
---

# THE VIBECODE WORKFLOW

Whenever you are asked to write, modify, or refactor code under this skill, you MUST follow this strict multi-agent loop. You are strictly forbidden from directly writing code to the project files until it has passed this exact audit process.

## STEP 1: PROPOSE
Draft the proposed code changes in your internal reasoning/context based on the project requirements, architecture, and any relevant domain skills (e.g. security, data integrity, framework guidelines, or UI principles).

## STEP 2: AUDIT (SUBAGENT)
Do NOT write the code to the filesystem yet. Instead, use the `invoke_subagent` tool to spawn an adversarial auditor subagent.
- **Model:** You MUST set the subagent's Model argument to `flash`.
- **Role:** Set to `"Adversarial Code Auditor"`.
- **Instructions:** Command the subagent to evaluate the proposed code against any relevant project skills, domain best practices, edge cases, type-safety, concurrency, security boundaries, and data integrity.
- **Payload:** Provide the complete proposed code diff and implementation details to the subagent for a rigorous, adversarial review.

## STEP 3: THE LOOP
Wait for the subagent's response.
- **If REJECTED / ISSUES IDENTIFIED:** You must address and fix all defects based on the auditor's feedback and submit the revised implementation to the subagent again. You must repeat this loop until the subagent explicitly approves.
- **If APPROVED:** You may proceed to use your file editing tools (`write_to_file`, `replace_file_content`) to apply the clean, verified code to the project.

Never bypass this loop. High code quality, security, and regression prevention depend on it.
