<div align="center">

# 🛡️ Vibecode

**The Propose-Audit-Write Multi-Agent Loop for Bulletproof Vibe Coding**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skill](https://img.shields.io/badge/Agent%20Skill-Antigravity%20%7C%20Claude%20%7C%20Codex-brightgreen)](SKILL.md)
[![Workflow](https://img.shields.io/badge/Workflow-Multi--Agent%20Adversarial%20Loop-orange)](#how-it-works)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-purple.svg)](https://github.com/sharooz007/vibecode/pulls)

*Stop letting AI agents write unverified code directly to your files. Put an adversarial auditor in the loop.*

[Quickstart](#installation--setup) • [The Workflow](#how-it-works) • [Auditor Checklist](#what-the-adversarial-auditor-inspects) • [Example Trace](examples/audit-trace.md) • [Launch Kit](LAUNCH_KIT.md)

---

</div>

## 💥 The Problem with Raw "Vibe Coding"

"Vibe coding" has unlocked unprecedented development speed, but single-pass AI coding comes with severe pitfalls:

- ❌ **Blind File Overwrites:** AI agents draft code and directly blast changes onto disk in one shot.
- ❌ **Silent Regressions:** Agents frequently delete critical edge cases, race condition guards, or unrelated helper functions.
- ❌ **Hallucinated Invariants:** Security rules, boundary checks, and strict database constraints get silently bypassed.
- ❌ **Confirmation Bias:** The model that writes the code is notoriously bad at catching its own logic flaws during the same generation turn.

**The result:** You spend more time reviewing git diffs, chasing mysterious runtime crashes, and fixing subtle bugs than building features.

---

## ⚡ The Solution: Vibecode

**Vibecode** is an open agent skill that enforces a strict **Propose-Audit-Write** multi-agent loop.

Under Vibecode, an AI assistant is **strictly forbidden** from touching your filesystem until a specialized, adversarial auditor subagent (`Adversarial Code Auditor`) rigorously reviews the proposal, attacks edge cases, and issues an explicit sign-off.

```
       ┌────────────────────────┐
       │   Developer Prompt     │
       └───────────┬────────────┘
                   │
                   ▼
       ┌────────────────────────┐
       │     STEP 1: PROPOSE    │ ───► Drafts code in internal memory
       │    (Lead Developer)    │      (ZERO filesystem writes allowed)
       └───────────┬────────────┘
                   │
                   ▼
       ┌────────────────────────┐
       │     STEP 2: AUDIT      │ ───► Spawns Adversarial Auditor Subagent
       │  (Adversarial Auditor) │      (Evaluates security, concurrency, invariants)
       └───────────┬────────────┘
                   │
         ┌─────────┴─────────┐
         │ Approved by Audit?│
         └─────────┬─────────┘
        NO ◄───────┴───────► YES
         │                    │
         ▼                    ▼
┌──────────────────┐  ┌────────────────────────────────┐
│ STEP 3: REVISE   │  │ STEP 4: WRITE                  │
│ Address flaws &  │  │ Commit clean, verified code to │
│ resubmit to loop │  │ project files on disk          │
└──────────────────┘  └────────────────────────────────┘
```

---

## 🔄 How It Works

### Step 1: Propose
The primary coding agent analyzes your prompt and drafts the required changes entirely in internal working memory. It checks project architecture, database schemas, and style rules, but **cannot edit files yet**.

### Step 2: Adversarial Audit (`invoke_subagent`)
The primary agent spawns an adversarial subagent using a fast, high-reasoning model:
- **Role:** `"Adversarial Code Auditor"`
- **Model:** `flash` (fast, unbiased, zero emotional attachment to the code)
- **Payload:** The full proposed diff, target file context, and domain invariants
- **Instructions:** Ruthlessly hunt for bugs, race conditions, type mismatches, security vulnerabilities, schema violations, and accidental code deletions.

### Step 3: The Audit Loop
The primary agent awaits the auditor's verdict:
- **If REJECTED:** The auditor specifies exact file locations, failure scenarios, and attack vectors. The primary agent must repair the flaws and re-submit the revised diff to the auditor.
- **Repeat:** The loop continues until the auditor explicitly returns an **APPROVED** verdict.

### Step 4: Write
Only after obtaining full auditor approval is the agent allowed to invoke file-writing tools (`write_file`, `edit_file`) to apply the verified changes to disk.

---

## 🔎 What the Adversarial Auditor Inspects

The auditor evaluates every proposed diff against 6 critical pillars:

| Category | What Is Inspected |
| :--- | :--- |
| **🛡️ Security** | Injection flaws (SQL, XSS, Command), SSRF, auth bypass, secret leaks, missing authorization checks |
| **⚡ Concurrency & Race Conditions** | Unsafe async states, non-atomic database reads/writes, stale closures in React, missing locks |
| **🧱 Domain Invariants** | Financial accuracy (floats vs integers/decimals), soft-delete rules, audit logging, schema integrity |
| **🧹 Non-Destructive Editing** | Ensuring existing comments, helper utilities, and unrelated methods are preserved |
| **📐 Type Safety & Contracts** | Zod/TypeScript schema consistency, null/undefined safety, unhandled promise rejections |
| **🚀 Edge Cases** | Malformed inputs, zero values, network dropouts, empty arrays, unicode boundary strings |

---

## 📊 Comparison: Raw Vibe Coding vs. Vibecode

| Feature | Raw Vibe Coding | With Vibecode |
| :--- | :---: | :---: |
| **File Safety** | ❌ Blindly overwrites files | ✅ Zero disk writes before audit |
| **Review Process** | ❌ None (or self-affirmation) | ✅ Independent adversarial subagent |
| **Model Separation** | ❌ Same model context | ✅ Dedicated auditor subagent (`flash`) |
| **Regression Rate** | ⚠️ High (silent code deletions) | 🛡️ Extremely low (invariant verification) |
| **Security Boundaries**| ⚠️ Easily hallucinated away | 🔒 Strictly tested before application |
| **Audit Trail** | ❌ Silent file edits | 📝 Full transparent reasoning trail |

---

## 🚀 Installation & Setup

### 1. Google Antigravity / Gemini CLI
Clone or copy this skill into your user or project skills folder:

```bash
# Global configuration (available in all projects)
git clone https://github.com/sharooz007/vibecode.git ~/.gemini/config/skills/vibecode

# Or project-specific configuration
mkdir -p .gemini/skills
git clone https://github.com/sharooz007/vibecode.git .gemini/skills/vibecode
```

### 2. Claude Code
Install to your Claude Code skills or rules directory:

```bash
mkdir -p ~/.claude/skills/vibecode
cp /path/to/vibecode/SKILL.md ~/.claude/skills/vibecode/
```

### 3. Cursor / Windsurf / Codex / Custom Agents
Add the contents of [`SKILL.md`](SKILL.md) directly to your project's `.cursorrules`, `.windsurfrules`, or system prompt instructions.

---

## 💡 Usage

Trigger Vibecode explicitly using the slash command or by asking your agent to use the skill:

```text
/vibecode Implement the Stripe checkout webhook endpoint in src/app/api/webhook/route.ts
```

Or naturally in prompt:
```text
Please refactor our user authentication flow using the vibecode skill to ensure zero regressions and adversarial verification.
```

The agent will automatically initiate the Propose-Audit-Write loop and output the audit feedback transparently in your chat.

---

## 📚 Repository Structure

```
vibecode/
├── SKILL.md              # Official skill specification and instructions
├── README.md             # Complete documentation and architectural overview
├── LAUNCH_KIT.md         # Ready-to-publish social threads (X, LinkedIn, Reddit, HN)
├── LICENSE               # MIT License
├── .gitignore            # Clean git defaults
└── examples/
    └── audit-trace.md    # Real-world simulation of an adversarial rejection & repair
```

---

## 🤝 Contributing

Contributions, auditor rule expansions, and integrations with other agent harnesses are very welcome!
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/auditor-rules`)
3. Commit your changes (`git commit -m 'Add new invariant checks for Postgres migrations'`)
4. Push to the branch (`git push origin feature/auditor-rules`)
5. Open a Pull Request

---

## 📄 License

This project is open-source under the [MIT License](LICENSE).

Crafted with care by [Sharooz](https://github.com/sharooz007).
