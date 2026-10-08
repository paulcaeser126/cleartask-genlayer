# ClearTask — GenLayer Studio Build Specification

Prepared: 2026-10-08  
Target: standalone GenLayer Intelligent Contract in GenLayer Studio Next Dev  
Release candidate: ClearTask 2.1 recovery patch; upgrade finalized in Studio Next Dev, with live timeout recovery still awaiting its deadline and end-to-end adjudication/payout validation

## Product contract

One instance represents one milestone. The client deploys immutable title and rubric, funds the instance with GEN, and assigns the first non-client worker who submits. Validators independently evaluate the same fixed rubric and submitted text. The contract stores the accepted structured decision and routes the full deposit to the worker on ACCEPT or back to the client on REJECT or INDETERMINATE after the challenge period. The app has one evidence-bearing appeal, one normal worker revision, and up to two worker refreshes after an unresolved review deadline.

## Runtime and target

- Source: `outputs/ClearTask.py`.
- Studio Next Dev build: `v0.123.0-rc.7`, chain ID `61997`.
- Exact runner dependency: `py-genlayer:5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng`.
- The documented current runner is absent from this Studio RC; do not silently substitute it. The RC7 source uses the API supported by the pinned runtime, `gl.vm.run_nondet_default`. Validators must independently check the leader's specific proposal; they must never accept a leader-only decision.
- Fresh Studio verification instance: `0xDba641391485A7697E22ef794F488e256c3f3DA6`; deployment transaction `0x9ef9a77f31c5634ee216826109f3f3b6c315e7e2a50164a440d423d91dd90517` and final review-guard code upgrade `0xa2418ad03c698325a0070d308c5f151f967294879d15b7fb5c85e2211e4f3ddb` finalized with accepted consensus. A fresh read preserved `OPEN` with zero escrow. The earlier completed instance at `0x5DEe1e4c91437Fc6e3110069d36D0682FC79ce4C` is historical and must not be upgraded to this different storage layout.

## Lifecycle and authorization

| State | Allowed action | Caller and effect |
|---|---|---|
| `OPEN` | `fund_escrow()` | Client deposits one positive payable amount; stored in wei. |
| `OPEN` | `submit_work(submission, evidence)` | Non-client submits only after funding; sender becomes worker. |
| `OPEN` | `cancel()` | Client may cancel before work; funded escrow becomes client-claimable. |
| `SUBMITTED` | `revise_submission(...)` | Worker may make one ordinary revision; previous work/evidence remain in public state. |
| `SUBMITTED` | `refresh_submission_after_timeout(...)` | Worker may refresh unresolved work up to two times after the seven-day review deadline; each refresh restarts the review window. |
| `SUBMITTED` | `adjudicate()` | Client during the seven-day review period; anyone after it. |
| `DECIDED` | `appeal(reason, additional_evidence)` | Once within seven days: client for ACCEPT; worker for REJECT or INDETERMINATE. |
| `APPEALED` | `adjudicate()` | Either milestone party during the seven-day appeal-review period; anyone afterward. |
| `DECIDED` | `finalize_decision()` | Anyone after the appeal period; sets the fixed beneficiary and makes full escrow claimable. |
| `SETTLED` or `CANCELLED` | `claim_payout()` | Fixed beneficiary only; emits a finalized external GEN transfer and marks the message sent. |

No rubric edits, multiple workers, more than one application appeal, or unbounded resubmission are supported. If nondeterministic consensus fails, the write does not commit. While state remains `SUBMITTED`, the worker has up to two time-gated refreshes after the review deadline to add evidence; each restarts the review window. A third timeout failure still requires a new instance or a separately reviewed recovery design.

## Decision, score, and grade

The rubric parser requires one through eight consecutive criteria (`C1` through `C8`), each with a nonempty bounded description. Model output must use the fixed verdict/reason enums and classify every criterion exactly once into `criteria_met`, `criteria_not_met`, or `missing_evidence`. ACCEPT requires all criteria met; REJECT requires affirmative unmet evidence; INDETERMINATE requires evidence gaps and no affirmatively unmet criterion.

The leader uses the fixed prompt and JSON-encoded user inputs. Each validator independently checks whether the leader's specific verdict, reason, and criterion classifications are defensible under the fixed rubric and evidence. Validators return only a boolean, avoiding a competing free-form verdict while retaining substantive review. The deterministic boundary validates the decision structure again before state writes. No free-form model narrative is persisted.

Criteria have equal weight. Integer score is `100 * met_count // criteria_count`. Grade thresholds are A ≥ 90, B ≥ 80, C ≥ 70, D ≥ 60, else F; an INDETERMINATE verdict reports grade `INCOMPLETE`. Grade is criterion coverage and is not interchangeable with the verdict.

## Native payments

- GEN is denominated in wei; `fund_escrow()` reads `gl.message.value` as `u256` and permits one client deposit only.
- No payout is available until the final decision is settled after the application appeal period. ACCEPT sets the worker as beneficiary; REJECT and INDETERMINATE set the client. Cancellation before submission refunds the client.
- `claim_payout()` uses a GenLayer EVM contract interface to emit a finalized external transfer to the immutable beneficiary address. The contract does not accept a caller-selected recipient or partial payout.
- GenLayer documentation states that outgoing value is deducted when a message is emitted and a failed child transaction is not automatically refunded. Studio has simulated balances and no EVM layer or ghost contracts. The payable ABI compiles and deploys in Studio Dev, but neither the actual GEN payout nor child-transfer failure recovery has been validated there. This RC is not cleared for production funds until that path is exercised on a persistent network and reviewed independently.

## Evidence and threat model

The contract judges only bounded user-submitted text. It does not fetch URLs or verify artifact existence, source contents, identity, or real-world facts. Submitted text is untrusted and may contain prompt injection. The prompt labels it as data, requests no outside information, and tells the model not to claim verification. It also explicitly treats a URL, file path, line number, transaction hash, or claimed test/deployment result as a reference rather than proof unless the actual excerpt or output is included. This reduces instruction injection and unsupported acceptance, but does not authenticate evidence or guarantee model agreement. Users should publish observable criteria and include the actual evidence text.

## Exact Studio ABI

- View: `get_state() -> str` exposes task terms, client/worker, all lifecycle state, current and initial grade/verdict, criterion arrays, timestamps/deadlines, escrow amount in wei, recipient, and payout status.
- Payable write: `fund_escrow() -> None`.
- Writes: `cancel()`, `submit_work(str, str)`, `revise_submission(str, str)`, `refresh_submission_after_timeout(str, str)`, `adjudicate()`, `appeal(str, str)`, `finalize_decision()`, `claim_payout()`.

## Verification record and limits

- Static GenVM lint: passed (3 checks).
- Local mocked unit tests: 60 passed across standard and short-window profiles after the timeout-refresh change. Coverage includes the verdict partition, proposal validation and malformed validator responses, failed provider and disagreement paths, client/worker roles, escrow gating, bounded revisions and timeout refreshes, one appeal, score/grade derivation, payout recipient guards, and cancellation refund logic. The suite does not execute GenVM consensus or native transfer children.
- Local GenVM SDK validation cannot read the cached RC7 runner artifact (`WinError 5`). A later on-chain upgrade `0x3ebcd693ea9ae3e0be9cc1f63e8b186164bef2697c6c106f844e4e71c8c4a74d` finalized. Its funded adjudication attempt `0xef9d9d797bfb2058cb8e5ab70e4ceafd750bfef974ea7ef4cfc3158e4ed2ea7a` finalized `UNDETERMINED`: the leader proposed ACCEPT, two validators agreed, and three disagreed. The contract remained `SUBMITTED` with 15 GEN held. The latest adjudication `0xd72951ba12ef324a845b3c45f7d0c2b9b237b7318e76c76887b19dd16819dae0` also finalized `UNDETERMINED`; the explorer shows three validator rejections, one acceptance, and one idle validator with successful GenVM execution. The evidence-reference prompt change was later deployed in upgrade `0x9e7ab9d27bd135d7240a7baae640a3746bf6a60a92dfec24524c2c23b7ae13b0`. SDK semantic validation remains blocked by the same Windows cache permission error.
- Studio deployment: accepted and finalized, contract address `0xDba641391485A7697E22ef794F488e256c3f3DA6`; no escrow value deposited. A full-consensus `submit_work` call with zero escrow left the state `OPEN` and recorded no worker or submission. A `0.001 GEN` payable UI test failed before submission because Studio attempted to convert the fractional amount to `BigInt`. Faucet requests for 1 and 10 simulated GEN produced no visible balance change; the wallet remained at `0.1 GEN`. The deployment validates ABI, constructor parsing, and the zero-escrow gate; it does not validate funded adjudication, appeal consensus, or external payout.
- The earlier 19-case suite, source, and transactions for the first contract remain documented in the previous Studio/dev history; they do not validate this new storage model.

## Status update — 2026-10-08

This update supersedes older deployment, funding, and adjudication status statements above. The active instance `0xDba641391485A7697E22ef794F488e256c3f3DA6` remains `SUBMITTED` with 15 GEN held. The bounded recovery upgrade `0x174a1a638e861ac807ffc90410f755a0b45b2b15a7a0684232eb34da0ec1b918` is finalized and Studio exposes `refresh_submission_after_timeout(...)`. The evidence-reference prompt upgrade `0x9e7ab9d27bd135d7240a7baae640a3746bf6a60a92dfec24524c2c23b7ae13b0` finalized; however, the subsequent funded adjudication `0xe06e79d6f6ab8dfc57c84a3fcaed1f1dc9fcf04ed825de5f559a779b0f626874` finalized `UNDETERMINED`. The explorer output proposed INDETERMINATE with all five criteria missing; three validators disagreed and two were idle. No decision or payout was recorded. A bounded, time-gated worker refresh is deployed in finalized upgrade [`0x174a1a638e861ac807ffc90410f755a0b45b2b15a7a0684232eb34da0ec1b918`](https://explorer-studio-next.genlayer.com/tx/0x174a1a638e861ac807ffc90410f755a0b45b2b15a7a0684232eb34da0ec1b918) and appears in Studio's Write Methods list; it becomes callable only after the review deadline. The build remains a Studio Dev test and is not submission-ready for real funds. See [`../docs/CHECKPOINT.md`](../docs/CHECKPOINT.md) for the full transaction record, limits, and next steps.




## Status update — 2026-10-09

The bounded `refresh_submission_after_timeout` upgrade finalized on the active instance in `0x174a1a638e861ac807ffc90410f755a0b45b2b15a7a0684232eb34da0ec1b918`; it did not resolve the existing held escrow or validator disagreement. The new `outputs/ClearTask-SameDay-Demo.py` profile sets review and appeal windows to 15 minutes. Both contract variants pass the 60-case mocked guard suite and GenVM lint. Studio recognizes the demo constructor schema, and a milestone title/rubric are staged, but no new deployment or deposit has been submitted. Native transfers and live consensus remain unverified. This profile is for a controlled Studio Dev demo, not production use.
