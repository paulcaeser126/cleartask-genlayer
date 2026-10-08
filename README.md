# ClearTask — GenLayer Intelligent Contract

ClearTask is a standalone GenLayer contract for a single milestone. A client defines a fixed rubric and funds GEN escrow; a worker submits text and evidence; GenLayer validators review the submission; and the contract tracks a verdict, score, grade, appeal, settlement, and payout claim.

## Checkpoint status — 2026-10-09

**Not ready for production funds or a claim of successful adjudication.** The original Studio Dev instance `0xDba641391485A7697E22ef794F488e256c3f3DA6` still holds 15 GEN in `SUBMITTED`; funded adjudications finalized `UNDETERMINED`. The bounded worker recovery method is now finalized in an upgrade to that instance, but it has not resolved validator disagreement or settled the old escrow.

A separate `outputs/ClearTask-SameDay-Demo.py` uses 15-minute review and appeal windows. Its source passes the local guard suite and GenVM lint, and Studio Next recognizes its constructor. The file and milestone inputs are staged in Studio, but **the fresh instance has not been deployed or funded**. The old escrow remains separate. Live consensus and native payout behavior remain unverified; use this profile only for a controlled Studio Dev demonstration.

See [docs/CHECKPOINT.md](docs/CHECKPOINT.md) for the full recorded project and transaction history, current blockers, verification record, and next work.

## Repository contents

- `outputs/ClearTask.py` — GenLayer-native contract source, pinned to the Studio Dev runner.\n- `outputs/ClearTask-SameDay-Demo.py` — separate 15-minute Studio Dev demonstration profile; not deployed.
- `outputs/ClearTask-Guide.md` — operator and lifecycle guide.
- `outputs/ClearTask-Studio-Build-Spec.md` — implementation and compatibility specification.
- `tests/unit/test_cleartask_guards.py` — mocked-boundary unit tests.\n\nLatest local verification: 60 mocked guard tests pass across both source variants; GenVM lint passes for both. These checks do not prove validator consensus or native GEN payout behavior.

## Target environment

- GenLayer Studio Next Dev build `v0.123.0-rc.7`, chain ID `61997`.
- Runner: `py-genlayer:5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng`.
- Test instance: [`0xDba641391485A7697E22ef794F488e256c3f3DA6`](https://explorer-studio-dev.genlayer.com/address/0xDba641391485A7697E22ef794F488e256c3f3DA6).

This is a Studio Dev test instance. Do not use real or production funds until consensus, native payout behavior, child-transfer failure handling, and security review are complete.

