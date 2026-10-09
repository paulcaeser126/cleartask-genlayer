# ClearTask — GenLayer milestone escrow demo

ClearTask is a single-milestone escrow contract. The client sets a task title and numbered rubric, funds GEN escrow, and the designated worker submits work and evidence. GenLayer validators evaluate the submission; the contract tracks the result, review and appeal windows, settlement, and payout claim.

## Current Studio Dev deployment

The corrected short-window source is `outputs/ClearTask-SameDay-Demo-Ready.py`. Its deployment finalized with consensus accepted:

- Contract: [0xB9B11468637d3aF6685A0adeAA640487Fa9BCC2B](https://explorer-studio-next.genlayer.com/address/0xB9B11468637d3aF6685A0adeAA640487Fa9BCC2B)
- Deployment transaction: [0x6c0c380cc921326f90515acb427b76ef1a522481e7c8deafab8914c0bc04f7a8](https://explorer-studio-next.genlayer.com/tx/0x6c0c380cc921326f90515acb427b76ef1a522481e7c8deafab8914c0bc04f7a8)
- Deployer and contract client: `0xB4E3e2D252Ce070e2289c62c9A09265b296FB782`
- Designated worker: `0x314C92977Ebc38Afe69dc643eF670cF4Dc5cAE17`
- Environment: GenLayer Studio Next Dev, chain ID `61997`

**This is not yet a complete submission.** The owner chose to keep the existing instance and use its deployer, `0xB4…B782`, as the client. A live `get_state()` read shows the milestone is `OPEN`, has zero GEN escrow, and records the designated worker. A successful call from that worker has not yet been verified. Fund only this Studio Dev test instance from the `0xB4…B782` client account, and use test funds only.

This is a Studio Dev demonstration only. The older contract at `0xDba641391485A7697E22ef794F488e256c3f3DA6` is separate and still holds the previously reported 15 GEN test escrow; this deployment did not move or release those funds. Do not use real or production funds.

## Submission checklist

| Rubric item | Status | Evidence / remaining work |
| --- | --- | --- |
| C1 — public source repository | **Pending** | The owner approved public visibility, but GitHub requires the owner to reauthenticate before the change can be completed. |
| C2 — README with purpose, deployment steps, constructor inputs, and methods | **Prepared** | This README documents all four; reviewers can access it after repository visibility is changed. |
| C3 — successful Studio Dev deployment and address | **Complete** | Address and finalized, accepted transaction are linked above. |
| C4 — successful worker contract call | **Pending** | The designated worker must submit work from `0x314C…AE17` after the client funds this instance. |

The deployed instance's client is `0xB4…B782`, which the owner selected for this demonstration. No redeployment is needed for this choice.

## Repository contents

- `outputs/ClearTask-SameDay-Demo-Ready.py` — corrected source used by the deployment above; pins the GenVM runner and restricts submissions to the designated worker.
- `outputs/ClearTask-SameDay-Demo-Ready-Guide.md` — constructor inputs, wallet roles, links, and remaining demo steps.
- `outputs/ClearTask-SameDay-Demo.py` — earlier short-window draft.
- `outputs/ClearTask.py` — main contract implementation and test target.
- `outputs/ClearTask-Guide.md` and `outputs/ClearTask-Studio-Build-Spec.md` — earlier implementation documentation.
- `tests/unit/test_cleartask_guards.py` — unit tests for guard and state-transition behavior.

## Constructor inputs used

**Title:** `ClearTask GenLayer Contract Demo`

**Rubric:**

```text
C1: The worker provides a public repository containing the standalone GenLayer contract source. | C2: The README explains the contract purpose, deployment steps, constructor inputs, and public methods. | C3: The contract successfully deploys in GenLayer Studio Next Dev, and its address is provided. | C4: The worker provides transaction details showing at least one successful contract call.
```

Rubric criteria use consecutive IDs (`C1`, `C2`, …), each followed by a description. The owner approved public visibility for this repository. Public visibility exposes all repository files and commit history, so review those contents before sharing the repository link.

## Deploy and run in Studio Dev

1. Open GenLayer Studio Next Dev and import `outputs/ClearTask-SameDay-Demo-Ready.py`.
2. Connect the wallet that should be the contract client. The deploying wallet becomes `client` and alone can fund or cancel the milestone.
3. Deploy a new instance with the title and rubric above. Verify the Explorer record is a `Deploy` transaction with `FINALIZED`, GenVM `SUCCESS`, and consensus `Accepted`, then record the new address and transaction link.
4. The client calls `fund_escrow()` with a positive Studio Dev GEN value.
5. The designated worker calls `submit_work(submission, evidence)` with non-empty text. Verify this separate transaction is a successful `Call` from the worker.
6. The client reviews with `adjudicate()`. The worker may call `appeal(reason, additional_evidence)` after a rejection or indeterminate result; the client may appeal an accepted result. After the appeal window, call `finalize_decision()`. The designated payout recipient calls `claim_payout()` when the state permits it.

## Public methods

- `get_state()` — read the milestone, worker, escrow, submission, review, and payout state.
- `fund_escrow()` — payable; client only, and only once for a positive amount.
- `cancel()` — client only, before work is submitted.
- `submit_work(submission, evidence)` — designated worker only, after funding.
- `revise_submission(submission, evidence)` — designated worker only, while review is pending and within the revision allowance.
- `refresh_submission_after_timeout(submission, evidence)` — designated worker only, after the review window expires and within the revision limit.
- `adjudicate()` — evaluate the submission against the rubric using validator consensus.
- `appeal(reason, additional_evidence)` — one appeal by the party permitted for the verdict.
- `finalize_decision()` — finalize after the appeal period ends.
- `claim_payout()` — transfer the claimable escrow to the recorded recipient.

## Verification

`genvm-lint lint outputs/ClearTask-SameDay-Demo-Ready.py` passes its three AST checks. `pytest tests/unit/test_cleartask_guards.py -q` reports 94 passed and 2 skipped. The `genvm-lint check` SDK semantic-validation step could not read the local cached SDK due to `WinError 5`; the Studio deployment itself finalized with consensus `Accepted`. These checks do not verify live validator adjudication or payout behavior. The successful worker call and correct client-controlled lifecycle remain outstanding.

