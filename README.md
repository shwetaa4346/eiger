# Eiger

[![CI](https://github.com/kkmookhey/eiger/actions/workflows/ci.yml/badge.svg)](https://github.com/kkmookhey/eiger/actions/workflows/ci.yml)

# Eiger Security Assessment — Network Intelligence Technical Screen

**Candidate:** Shweta Tambe  
**Assessment:** Eiger Technical Screen  
**Organization:** Network Intelligence

---

## Candidate Submission

This repository contains my completed technical assessment for the Network Intelligence Eiger security exercise.

The assessment was performed in the authorized Eiger AI Security Lab environment. The work focused on identifying and reproducing a security weakness, implementing a security control, and validating the behavior before and after remediation.

### Primary Assessment Focus

**Layer:** M5 – Agent / Excessive Agency  
**Vulnerability:** Excessive Agency / Broken Tool Authorization

The assessment demonstrates how an AI agent can perform a high-impact action when application-level authorization does not independently verify whether the requested action is permitted.

The specific scenario involved attempting to move funds to an account that was not owned by the current user.

---

## Vulnerability Summary

In the vulnerable implementation, tool authorization could be bypassed when tool-scope enforcement was disabled.

The vulnerable authorization logic allowed tool calls without independently verifying whether the target account belonged to the current session.

This creates a security boundary failure because:

> An AI agent deciding to call a tool is not equivalent to the user being authorized to perform the action.

If an AI agent is given access to sensitive financial tools, relying on the model to make the authorization decision can result in unauthorized transactions or other high-impact actions.

---

## Attack Demonstration

The attack was performed against the vulnerable M5 implementation.

The test flow was:

1. Open the M5 – Excessive Agency exercise.
2. Reset the test accounts.
3. Identify the account belonging to the current session.
4. Use the AI agent to request a transfer to an account not owned by the current user.
5. Observe the generated tool call.
6. Verify the behavior of the vulnerable authorization logic.
7. Validate the result using the Eiger validation mechanism.

The vulnerable behavior demonstrated that the agent could use application privileges to perform an action outside the user's ownership boundary.

---

## Security Fix

The authorization logic was hardened by introducing deterministic ownership checks before sensitive tool actions are permitted.

For money-moving operations, the application verifies whether the destination account belongs to the current session before allowing the action.

The same authorization principle was applied to sensitive account operations such as account email changes.

The resulting security boundary is:

```text
User Request
     ↓
AI Agent
     ↓
Tool Selection
     ↓
Tool Arguments
     ↓
Deterministic Authorization
     ↓
Ownership Check
     ↓
Sensitive Action
```

---

## Validation

The same attack scenario was tested before and after the security control was applied.

| Test State | Result |
|---|---|
| Vulnerable implementation | Attack succeeded |
| Hardened implementation | Unauthorized action was blocked |
| Validation mechanism | Eiger validation mechanism |

Supporting before/after screenshots, recordings, and validation evidence are included in the assessment report and repository evidence.

---

## Impact

Excessive agency and broken tool authorization can allow an AI agent to misuse legitimate application privileges.

Potential impacts include:

- Unauthorized financial transfers
- Unauthorized account changes
- Cross-account actions
- Privilege abuse
- Data modification
- Financial loss
- Confused-deputy attacks

The risk is particularly significant when AI agents are connected to tools capable of performing irreversible or high-impact operations.

---

## What the Fix Covers

The implemented authorization control ensures that sensitive actions are checked against the current user's ownership boundary before execution.

It specifically addresses the identified authorization weakness in the Eiger M5 exercise by preventing the agent from performing protected actions against resources that do not belong to the current session.

---

## What the Fix Does Not Cover

The implemented fix addresses the identified tool-authorization and ownership-checking weakness. It should not be considered a complete security solution for an AI agent or financial application.

Additional controls would still be required, including:

- Least-privilege tool access
- Transaction and spending limits
- Strong authentication and authorization
- Additional confirmation for sensitive actions
- Human approval for high-impact operations
- Detailed tool-call audit logging
- Monitoring for abnormal agent behavior
- Protection against prompt injection and other AI-specific attacks
- Independent security controls around connected services

Therefore, deterministic authorization is one layer of defense rather than a complete replacement for defense-in-depth security.

---

## Evidence

Supporting evidence from the assessment is available in the repository.

The evidence includes before/after screenshots and validation results demonstrating the security testing performed during the assessment.

The complete evidence and demonstration are also documented in the technical assessment report.

---

## Technical Write-up

The complete technical assessment report is included with the submission.

The report contains the vulnerability analysis, exploitation steps, remediation, before/after evidence, screenshots, and validation results.

---

## Demo Video

**3-Minute Screen Recording:**  
[https://drive.google.com/file/d/1KAAqTJ8lIwTWOSQazY68yg039MCa-H87/view?usp=sharing]

The demonstration covers:

1. Vulnerable behavior
2. Attack execution
3. Security fix
4. Re-testing
5. Validation result
6. Security limitation
7. AI-assisted workflow

---

## AI Usage

AI tools, including ChatGPT, were used as a supporting learning and analysis assistant during the assessment.

AI assistance was used for:

- Understanding unfamiliar AI security concepts
- Interpreting Eiger lab behavior
- Exploring attack and mitigation approaches
- Troubleshooting implementation issues
- Interpreting validation and tool output
- Structuring technical documentation

The final implementation and security behavior were independently tested and verified using the Eiger validation mechanism.

---

## Responsible Use

All security testing documented in this repository was performed within the authorized Eiger AI Security Lab environment provided for this technical assessment.

No unauthorized external systems were targeted.

The Eiger environment is intentionally vulnerable and is intended for security training and assessment purposes only.

---
## ⚠️ Read this before you run it

**Eiger is deliberately vulnerable software. It exists to be attacked. It is not a product, and it is not safe to deploy.**

Every module ships real, working vulnerabilities on purpose — prompt injection, stored XSS, an agent tool layer that moves money with no authorization, RAG knowledge-base poisoning, MCP tool-description poisoning, unsafe pickle deserialization leading to remote code execution, a pinned dependency with a known critical CVE, and guardrail bypasses. **Some modules execute attacker-supplied code by design.** The `secure` flags demonstrate the fixes; they do not make the lab safe to expose.

Treat any Eiger instance as already compromised, and act accordingly:

- **Never run it on a host, network, or cloud account you care about.** Assume anyone who can reach it can execute code in its container and read anything that container can read.
- **Isolate it.** Throwaway host or dedicated cloud project, one container per participant, no route to production networks or internal services, and block the cloud metadata endpoint (`169.254.169.254`).
- **Never give it real credentials or real data.** Every fixture in this repo is synthetic and must stay that way. If you supply your own model API key (BYOK), use a disposable key with a spend cap and revoke it when the session ends.
- **Do not expose it to the public internet** for longer than a teaching session needs, and never without per-participant isolation.
- **Do not reuse this code in production.** Copying a pattern out of here into a real system will reproduce the vulnerability — that is what the pattern is for.

You are responsible for where you run this and for anything that happens as a result.

### No warranty

This software is provided **AS-IS, WITHOUT WARRANTY OF ANY KIND**, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and noninfringement. In no event shall the author or copyright holder be liable for any claim, damages, or other liability arising from, out of, or in connection with the software or its use. See [`LICENSE`](LICENSE) for the full text.

The vulnerabilities here are intentional and will not be "fixed." Security reports about the deliberate teaching vulnerabilities are out of scope; anything genuinely unintended is welcome as a normal issue.

---

> **Naming:** the lab, the in-fiction neobank, and all learner-facing product copy are **Eiger**; the assistant is **Iggy**. The Python package, environment variables, and fixed grading canaries retain their historical `halcyon`/`HALO` identifiers to avoid a compatibility-breaking rename.

## Doctrine (load-bearing)

1. **Validate the mechanism, not the model's words** — pass/fail is a query against an append-only audit log.
2. **One build + `SEC_*` flags** — `vulnerable` vs `secure` is a config flag; the diff is the lesson.
3. **Local floor, BYOK ceiling** — Ollama (keyless, default) or the participant's own key, selectable at runtime. Both online.
4. **Deterministic + resettable + self-service** — `/validate/{module}`, `/reset/{module}`, readiness on screen 1.

**Deployment:** hosted, container-per-participant app instances, a shared Ollama backend, and an external progress store; the same images dual-deploy to cloud (primary) and a local-LAN server (fallback).

## Run it locally

Everything runs from Docker Compose — the same images as the hosted lab. **Prereqs: Docker Desktop only** (no Python/Node needed).

```bash
git clone https://github.com/kkmookhey/eiger && cd eiger
docker compose up -d --build                          # web, db, ollama, 2 MCP servers
docker compose exec ollama ollama pull llama3.1:8b    # first run only (~4.9 GB)
open http://localhost:8000/                           # readiness check → learner-guided lab UI
```

All published Compose ports bind to `127.0.0.1` by default; Postgres and Ollama are available only inside the Compose network. See [`OPERATIONS.md`](OPERATIONS.md) before making any service reachable from another machine.

- **Day-1 modules** (L0 chatbot, L1 RAG) run **keyless** on the local Ollama.
- **Day-2 modules** (L2 agent, L3 MCP, L4 multi-agent) are **BYOK** — paste an OpenAI/Anthropic key in the UI (frontier models chain tool calls reliably; the keyless model shows the plumbing).
- Every module has an objective, **Check progress**, and **Reset attempt** in the UI. Security controls are labelled **Vulnerable ⇄ Hardened**. Learner progress is at `/progress?session=…`; the human-readable class board is `/attack-board` (`/board` remains the JSON API). First `/api/ask` is instant — the embedding model is baked into the image.
- Ports already taken on your box? Add a `docker-compose.override.yml` remapping the host ports while retaining `127.0.0.1` bindings.

## Status

**M1–M8 and the Treasury Heist capstone are built and merged. The learner-guided UI is live across the full L0→L5 attack surface (chatbot → RAG → agent → MCP → multi-agent → production guardrails). Next: user feedback, then the remaining Ops/fleet and course-material work.**

👉 **[`docs/STATUS.md`](docs/STATUS.md) is the detailed build and architecture status.** Participant and trainer material lives under [`docs/labs/`](docs/labs/).

Contributions are welcome; read [`CONTRIBUTING.md`](CONTRIBUTING.md) first. For unintended security problems, follow [`SECURITY.md`](SECURITY.md).

## License

MIT © 2026 KK Mookhey. See [`LICENSE`](LICENSE).

Provided **as-is, with no warranty** — and note the deliberate-vulnerability warning at the top of this file before deploying anything.
