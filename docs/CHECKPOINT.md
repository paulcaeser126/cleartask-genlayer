# ClearTask project checkpoint

**Recorded:** 2026-10-08  
**Project:** ClearTask, standalone GenLayer intelligent contract  
**Environment:** GenLayer Studio Next Dev, build `v0.123.0-rc.7`, chain ID `61997`  
**Status:** Deployed and funded for testing; adjudication consensus remains unresolved. Not ready for production funds or final submission.

## What the contract is intended to do

Each instance represents one milestone with a fixed title and one to eight observable criteria. A client funds the instance in GEN; a different address submits work and evidence; the contract asks GenLayer validators to assess the submission; and a structured verdict records criterion coverage, a deterministic score and grade, and a fixed payout beneficiary. The lifecycle supports one worker revision, one evidence-bearing appeal, cancellation before work submission, delayed settlement, and a fixed-recipient claim.

The source is pinned to the exact Studio-compatible runner:

`py-genlayer:5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng`

The deployed source uses `gl.vm.run_nondet_default` and a validator function that evaluates whether the leader's specific proposal is defensible and returns a boolean. The review prompt also treats URLs, file paths, line numbers, transaction hashes, and test/deployment claims as references rather than proof unless the relevant content is reproduced. This version was deployed in the finalized code upgrade `0x9e7ab9d27bd135d7240a7baae640a3746bf6a60a92dfec24524c2c23b7ae13b0`. The following funded adjudication still ended `UNDETERMINED`; therefore the prompt change did not resolve live consensus.

## Deployed test instance and state

- Instance: [`0xDba641391485A7697E22ef794F488e256c3f3DA6`](https://explorer-studio-dev.genlayer.com/address/0xDba641391485A7697E22ef794F488e256c3f3DA6)
- Milestone title: `ClearTask V2 Studio compatibility check`.
- Current on-chain lifecycle state at the latest Studio read: `SUBMITTED`.
- Escrow: `15 GEN` (`15000000000000000000` wei), `HELD`.
- Verdict: empty; score and grade unset.
- Appeal: not used.
- No payout recipient is set and no payout has been claimed.
- The worker used the one allowed revision. The revision transaction finalized successfully.

The wallet balance shown in Studio was 15.1 GEN; this UI figure is distinct from the contract's reported escrow field. The escrow field remains the authoritative milestone amount.

## Transaction history

Transaction status is taken from Studio transaction history and/or the Studio Dev explorer. Links below point to transaction details where available.

| Event | Transaction | Recorded result |
|---|---|---|
| Initial contract deployment | [`0x9ef9a77f31c5634ee216826109f3f3b6c315e7e2a50164a440d423d91dd90517`](https://explorer-studio-dev.genlayer.com/tx/0x9ef9a77f31c5634ee216826109f3f3b6c315e7e2a50164a440d423d91dd90517) | Finalized; contract instance created. |
| Earlier review-guard code upgrade | [`0xa2418ad03c698325a0070d308c5f151f967294879d15b7fb5c85e2211e4f3ddb`](https://explorer-studio-dev.genlayer.com/tx/0xa2418ad03c698325a0070d308c5f151f967294879d15b7fb5c85e2211e4f3ddb) | Finalized with accepted consensus. |
| Escrow funding | [`0x8dae2e2e57c5748316a1689c95dfa24a7fa0d7f21ad0e4eb73662cba80caaa82`](https://explorer-studio-dev.genlayer.com/tx/0x8dae2e2e57c5748316a1689c95dfa24a7fa0d7f21ad0e4eb73662cba80caaa82) | Finalized; 15 GEN shown held in contract state. |
| Worker revision | [`0x9916c1b50fbea1ff532930cf9556818ca0899e203c2d807251cba9cab642ba1c`](https://explorer-studio-dev.genlayer.com/tx/0x9916c1b50fbea1ff532930cf9556818ca0899e203c2d807251cba9cab642ba1c) | Finalized with accepted consensus. |
| Validator code upgrade | [`0x3ebcd693ea9ae3e0be9cc1f63e8b186164bef2697c6c106f844e4e71c8c4a74d`](https://explorer-studio-dev.genlayer.com/tx/0x3ebcd693ea9ae3e0be9cc1f63e8b186164bef2697c6c106f844e4e71c8c4a74d) | Upgrade submitted earlier in the iteration; Studio history later showed this row as pending. Treat finality as needing explorer confirmation before relying on it. |
| First funded adjudication | [`0xef9d9d797bfb2058cb8e5ab70e4ceafd750bfef974ea7ef4cfc3158e4ed2ea7a`](https://explorer-studio-dev.genlayer.com/tx/0xef9d9d797bfb2058cb8e5ab70e4ceafd750bfef974ea7ef4cfc3158e4ed2ea7a) | Finalized `UNDETERMINED`. Leader proposed ACCEPT; two validators agreed and three disagreed. No decision committed. |
| Latest proposal-focused validator upgrade | [`0x3b73962bc83e9526f33e00e906c3085fe2c3ee759da8f230d1e68fa041338e0d`](https://explorer-studio-dev.genlayer.com/tx/0x3b73962bc83e9526f33e00e906c3085fe2c3ee759da8f230d1e68fa041338e0d) | Explorer confirms `FINALIZED Upgrade`, normal execution, to the current instance. |
| Second funded adjudication after that upgrade | [`0xd72951ba12ef324a845b3c45f7d0c2b9b237b7318e76c76887b19dd16819dae0`](https://explorer-studio-dev.genlayer.com/tx/0xd72951ba12ef324a845b3c45f7d0c2b9b237b7318e76c76887b19dd16819dae0) | Explorer consensus tab shows `UNDETERMINED`, leader proposed `ACCEPT`, three validator votes disagreed, one agreed, and one validator was idle. GenVM execution was `SUCCESS` with empty stdout/stderr; this is semantic validator disagreement, not a contract runtime exception. No verdict committed; escrow remains held. |
| Evidence-reference prompt code upgrade | [`0x9e7ab9d27bd135d7240a7baae640a3746bf6a60a92dfec24524c2c23b7ae13b0`](https://explorer-studio-dev.genlayer.com/tx/0x9e7ab9d27bd135d7240a7baae640a3746bf6a60a92dfec24524c2c23b7ae13b0) | Finalized upgrade to the current instance; applied the stricter rule that references are not proof. |
| Third funded adjudication after the prompt upgrade | [`0xe06e79d6f6ab8dfc57c84a3fcaed1f1dc9fcf04ed825de5f559a779b0f626874`](https://explorer-studio-dev.genlayer.com/tx/0xe06e79d6f6ab8dfc57c84a3fcaed1f1dc9fcf04ed825de5f559a779b0f626874) | Finalized transaction, consensus result `UNDETERMINED`. GenVM execution succeeded; equivalence-principle output proposed `INDETERMINATE` with all five criteria in `missing_evidence`. The transaction did not change contract state: the finalized state view still reports `SUBMITTED`, blank verdict, and 15 GEN `HELD`. Explorer reports 3 validator rejections, 1 acceptance, 1 idle; execution fee was 0.000079 GEN.

Older historical addresses and transactions from the initial prototype are intentionally not treated as evidence for the current storage layout or payout path. See the build specification for the historical context.

## Verification completed

- `genvm-lint lint outputs/ClearTask.py --json`: passed, 3 checks.
- `pytest tests/unit/test_cleartask_guards.py -q`: 27 passed. These are mocked-boundary tests; they do not simulate live validator consensus or execute native value transfers.
- Contract deployment and code upgrades were accepted/finalized in Studio Dev. The prompt upgrade `0x9e7ab9d…` finalized; its follow-up adjudication `0xe06e79d…` finalized as a transaction but with `UNDETERMINED` consensus and no state write.
- Studio accepted the ABI and state view; zero-escrow guard behavior was tested in an earlier run.
- Local GenVM SDK validation was blocked because Windows returned `WinError 5` reading the cached RC7 SDK artifact.
- Python `py_compile` could not be rerun in the latest environment because the Python shim could not resolve `C:\Python314\python.exe`. Do not claim that check passed for the current revision based on the earlier checkpoint.
- No appeal, settlement, payout claim, or external GEN transfer has been validated end to end.

## Current blockers and risks

1. **Consensus:** Three funded adjudications have finalized `UNDETERMINED`, including one after the evidence-reference prompt upgrade. The latest proposed `INDETERMINATE` with all five criteria missing, but validator disagreement prevented the write. GenLayer docs state an undetermined transaction does not modify contract state. Repeating the unchanged call would incur another fee without addressing validator disagreement.
2. **Evidence and diagnostics:** The latest validator votes are visible, but the explorer does not expose each boolean validator's reasoning. The submission contains references to sources, transactions, and tests rather than the underlying excerpts/results. The deployed prompt correctly makes these missing evidence; however, validators still disagreed on that proposal. No outside artifacts are fetched by the contract.
3. **One revision is already consumed:** The assigned worker's sole revision finalized. Any different evidence may require a new test instance or a deliberate code-supported resubmission design; do not assume a second revision is available.
4. **Escrow safety:** The 15 GEN stays `HELD` while no decision is recorded. Do not call payout/finalize methods until the state and timing guards show they are valid.
5. **Native transfer behavior:** Studio Dev lacks the EVM layer / ghost contracts needed to validate external GEN transfers. The payout child message and failure/recovery behavior are unverified. This test deployment is not suitable for production funds.
6. **SDK validation:** Local SDK validation is blocked by runner cache access; resolve that environment issue or use a supported Studio validation path.

## Recommended next work

1. Do not repeat adjudication against the unchanged evidence. First diagnose validator disagreements with a minimal, reproducible test case and expose stable diagnostics where the supported GenLayer API allows it.
2. The latest decision did not commit, so the application appeal path is not open. The worker's sole revision is already consumed. A new instance with actual evidence, or a carefully reviewed upgrade that restores a safe worker resubmission path, is needed to continue the live milestone; do not claim the existing instance is complete.
3. Once a decision is actually recorded, verify `get_state()` at `Finalized` before attempting an appeal, settlement, or payout.
4. For the next instance, keep criteria short and observable and include the actual evidence text. Do not claim that links, source paths, transaction hashes, or test summaries were independently checked.
5. Before any production deployment, validate native GEN payout and failed-child handling on a persistent network and obtain independent contract review.

## Key files

- `outputs/ClearTask.py` — contract source.
- `outputs/ClearTask-Guide.md` — interaction guide and safety limits.
- `outputs/ClearTask-Studio-Build-Spec.md` — detailed implementation specification.
- `tests/unit/test_cleartask_guards.py` — guard and decision-structure tests.
- `tests/direct/` — direct-mode coverage.

## Scope of this checkpoint

This record captures the state visible in the workspace and Studio Next Dev on 2026-10-08. It does not assert that the milestone has passed, that a payout occurred, or that validator disagreement has been fixed. A finalized `UNDETERMINED` transaction is a completed transaction whose decision did not reach consensus; it is not an accepted milestone verdict.
