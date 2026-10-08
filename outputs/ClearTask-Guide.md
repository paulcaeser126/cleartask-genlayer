# ClearTask Studio Guide

ClearTask is a standalone GenLayer Intelligent Contract for one funded milestone. A client publishes a fixed rubric and deposits GEN. A worker submits work and evidence text. GenLayer validators independently review it and the contract records a verdict, criterion classifications, a deterministic score and grade, and a fixed payout recipient.

## Target and release status

- Target: GenLayer Studio Next Dev, Studio build `v0.123.0-rc.7`, chain ID `61997`.
- Runner pin: `py-genlayer:5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng`.
- Contract source: `outputs/ClearTask.py`.
- The exact pinned source compiles and deploys in Studio Next Dev. The new test instance is `0xDba641391485A7697E22ef794F488e256c3f3DA6`; deployment transaction `0x9ef9a77f31c5634ee216826109f3f3b6c315e7e2a50164a440d423d91dd90517` and final review-guard code upgrade `0xa2418ad03c698325a0070d308c5f151f967294879d15b7fb5c85e2211e4f3ddb` both reached Finalized with accepted consensus. A fresh state read after the upgrade preserved `OPEN` and zero escrow.
- The old delivery milestone at `0x5DEe1e4c91437Fc6e3110069d36D0682FC79ce4C` is historical and unchanged. Deploy this source as a fresh contract; do not upgrade that completed milestone.
- Static GenVM lint passes and `tests/unit/test_cleartask_guards.py` passes 27 mocked-boundary checks. A full-consensus zero-escrow `submit_work` call left an instance `OPEN` with no worker or submission, confirming the funding gate. The Studio amount widget rejected an earlier fractional `0.001 GEN` attempt before transaction submission; the client later funded the active test instance with 15 GEN. Local SDK validation is blocked by Windows `WinError 5` reading the cached RC7 runner. Studio has no EVM layer or ghost contracts, so the external GEN payout child message has not been exercised. This remains a Studio-preview test, not a production-ready escrow release.
- Current funded test instance: `0xDba641391485A7697E22ef794F488e256c3f3DA6`. A later worker revision finalized (`0x9916c1b50fbea1ff532930cf9556818ca0899e203c2d807251cba9cab642ba1c`), using the one allowed revision. The code upgrade `0x3ebcd693ea9ae3e0be9cc1f63e8b186164bef2697c6c106f844e4e71c8c4a74d` finalized, but funded adjudication `0xef9d9d797bfb2058cb8e5ab70e4ceafd750bfef974ea7ef4cfc3158e4ed2ea7a` finalized as `UNDETERMINED` (leader ACCEPT; two validators agreed, three disagreed). State remains `SUBMITTED`; 15 GEN remains `HELD`. The local source now includes a proposal-focused boolean validator, but that change is not yet deployed or Studio-validated.

## Deploy and operate

1. Open `outputs/ClearTask.py` in Studio Next and deploy a fresh instance with a concrete title and one to eight observable criteria. Use consecutive IDs such as `C1: ... | C2: ...`; describe outcomes that can be established from the supplied submission.
2. The client calls `fund_escrow()` as a payable write and attaches the full agreed amount in GEN. Amounts are wei (`1 GEN = 10^18 wei`). Funding is one-time, client-only, and must be positive.
3. A different address calls `submit_work(submission, evidence)`. Submission is blocked until escrow is funded. The client cannot submit to its own milestone.
4. The assigned worker may call `revise_submission(...)` once before adjudication. The original submission and evidence are kept in the state.
5. The client may call `adjudicate()` during the seven-day review period. After the deadline, anyone may call it to prevent client inaction from locking the worker indefinitely. Each validator independently checks the leader's specific proposal against the fixed rubric and submitted evidence. If consensus is undetermined, no decision is written and escrow remains held; retries cost transaction fees.
6. The losing party may call `appeal(reason, additional_evidence)` once during the seven-day appeal period: the client can challenge ACCEPT; the worker can challenge REJECT or INDETERMINATE. Both milestone parties can request the appeal adjudication during its review period; after seven days anyone can.
7. After the final decision's seven-day appeal period, anyone calls `finalize_decision()`. ACCEPT makes the full escrow claimable by the worker; REJECT and INDETERMINATE make it claimable by the client. A client may cancel before any submission and recover funded escrow.
8. The designated recipient calls `claim_payout()`. The method emits a GenLayer external transfer to that fixed address. Read `get_state()` and distinguish `HELD`, `CLAIMABLE`, and `SENT`.

## Result grading

Criteria have equal weight. `score` is the integer percentage of criteria classified as met (`met_count * 100 // criteria_count`). Grades are A (90–100), B (80–89), C (70–79), D (60–69), and F (0–59). An `INDETERMINATE` result is labeled `INCOMPLETE`, while its numeric score still reports the percentage supported as met. Verdict and grade answer different questions: one unmet criterion can produce a `REJECT` with a high score.

## State flow

`OPEN → SUBMITTED → DECIDED → SETTLED`

One permitted appeal changes `DECIDED → APPEALED → DECIDED`. A client can cancel an `OPEN` milestone, producing `CANCELLED`. A failed or undetermined adjudication transaction commits no decision; retry `adjudicate()` while the app state remains `SUBMITTED` or `APPEALED`.

## Trust and payment boundaries

- The contract evaluates only the text supplied by users. It does not fetch URLs, open repositories, check files, verify transaction claims, or prove that two wallet addresses belong to different people. JSON encoding and prompt rules reduce prompt-injection risk; they do not authenticate evidence or eliminate model error.
- Use observable criteria and public evidence references, and do not submit secrets or private data. Treat scores as rubric coverage, not a universal measure of work quality. Do not use this contract for employment, credit, insurance, legal, medical, or other high-impact decisions.
- GEN transfers to EOAs are external messages and execute at finality. The protocol documentation warns that if a child transfer fails, its value is not automatically refunded to the sending contract. Studio simulates balances and has no EVM layer or ghost contracts, so the payout path cannot be fully validated in Studio Next Dev. Do not put production funds in this preview. Before real-value use, validate the payout and failure/recovery path on a persistent GenLayer network and obtain an independent security review.
- GenLayer protocol appeals still govern adjudication and payout transactions; wait for finality before treating a result or external transfer as complete.

## Local verification

- `genvm-lint lint outputs/ClearTask.py`: passed (3 checks).
- `pytest tests/unit/test_cleartask_guards.py -q`: 27 passed. These tests mock GenVM and external messages; they do not prove native consensus or transfer behavior.
- Studio Next Dev: fresh contract deployment finalized with accepted consensus. Its ABI exposes the payable funding method and the expected lifecycle methods. No value was deposited in the preview instance; the Studio amount widget blocked fractional-GEN funding before transaction submission.

## Current checkpoint — 2026-10-08

This update supersedes older release and funding statements above. The current Studio Dev instance is `0xDba641391485A7697E22ef794F488e256c3f3DA6`, with `SUBMITTED` status and 15 GEN held. Its latest validator-focused code upgrade finalized, but the next funded adjudication finalized `UNDETERMINED`; no verdict or payout exists. The worker's one revision is already used. Do not retry adjudication until the validator disagreement and evidence are examined. This contract is not ready for production funds. See [`../docs/CHECKPOINT.md`](../docs/CHECKPOINT.md) for the full transaction history, blockers, and next work.
