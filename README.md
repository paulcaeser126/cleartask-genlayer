# ClearTask — GenLayer Intelligent Contract

ClearTask is a standalone GenLayer contract for a single milestone. A client defines a fixed rubric and funds GEN escrow; a worker submits text and evidence; GenLayer validators review the submission; and the contract tracks a verdict, score, grade, appeal, settlement, and payout claim.

## Checkpoint status — 2026-10-08

**Not submission-ready for real funds.** The contract is deployed on Studio Next Dev and holds a 15 GEN test escrow. Three funded adjudications finalized `UNDETERMINED`. The latest used the deployed evidence-reference prompt and proposed `INDETERMINATE` with all five criteria missing, but validators disagreed, so contract state did not change. The finalized state still reports `SUBMITTED`, blank verdict, and 15 GEN `HELD`. No payout was made. The latest execution fee was 0.000079 GEN.

See [docs/CHECKPOINT.md](docs/CHECKPOINT.md) for the full recorded project and transaction history, current blockers, verification record, and next work.

## Repository contents

- `outputs/ClearTask.py` — GenLayer-native contract source, pinned to the Studio Dev runner.
- `outputs/ClearTask-Guide.md` — operator and lifecycle guide.
- `outputs/ClearTask-Studio-Build-Spec.md` — implementation and compatibility specification.
- `tests/unit/test_cleartask_guards.py` — mocked-boundary unit tests.

## Target environment

- GenLayer Studio Next Dev build `v0.123.0-rc.7`, chain ID `61997`.
- Runner: `py-genlayer:5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng`.
- Test instance: [`0xDba641391485A7697E22ef794F488e256c3f3DA6`](https://explorer-studio-dev.genlayer.com/address/0xDba641391485A7697E22ef794F488e256c3f3DA6).

This is a Studio Dev test instance. Do not use real or production funds until consensus, native payout behavior, child-transfer failure handling, and security review are complete.
