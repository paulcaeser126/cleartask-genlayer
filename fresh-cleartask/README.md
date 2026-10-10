# ClearTask — clean GenLayer contract build

This is a new, standalone milestone escrow contract. It is separate from the earlier ClearTask deployment and does not reuse its address, state, or deployment steps.

## What it does

- The client creates a milestone with a title, acceptance rubric, and worker address.
- The client funds the escrow with GEN.
- The designated worker submits work and evidence.
- The client requests an intelligent evaluation. The leader and validators independently evaluate the same bounded inputs; they must agree on the fixed decision (`APPROVE`, `REVISE`, or `REJECT`) before the contract changes the milestone state.
- The worker may make one requested revision. Either party may use one appeal within 15 minutes of a final decision. A payout or refund cannot be claimed during that first appeal window. After the appeal is decided, the remaining party may claim immediately because the single appeal has been used.
- After an accepted decision, the worker claims an approval payout or the client claims a rejection refund. If the validators cannot agree, the parties can also agree to release the entire escrow to either party through a mutual settlement proposal and acceptance.

## Constructor inputs

| Field | Type | Requirement |
|---|---|---|
| `title` | `str` | Required; up to 120 characters |
| `rubric` | `str` | Required; up to 4,000 characters |
| `worker_address` | `str` | A 20-byte `0x` address; the deploying wallet becomes the client |

The contract is pinned to the documented GenLayer Python runner hash on the first line of `ClearTask.py`.

## Public methods

- `get_state()` — JSON text snapshot of the milestone and escrow state.
- `fund_escrow()` — client sends a non-zero GEN amount as the transaction value.
- `submit_work(submission, evidence)` — designated worker submits the initial work.
- `revise_submission(submission, evidence)` — worker uses the one permitted revision after `REVISE`.
- `adjudicate()` — client requests validator-checked evaluation of submitted work.
- `appeal(reason, additional_evidence)` — either party uses the one appeal within 15 minutes of a final decision.
- `claim_payout()` — worker claims escrow after an accepted `APPROVE` decision and, if no appeal was filed, after the 15-minute appeal window expires.
- `claim_refund()` — client claims escrow after an accepted `REJECT` decision and, if no appeal was filed, after the 15-minute appeal window expires.
- `cancel()` — client cancels before any work is submitted; funded GEN is returned.
- `propose_mutual_settlement(recipient_address)` — either party proposes paying all remaining escrow to the client or worker.
- `accept_mutual_settlement()` — the other party accepts the pending proposal.

## Build and deploy in Studio Next

1. Open [GenLayer Studio Next](https://studio-next.genlayer.com/run-debug) and select the intended network.
2. Create a new Python contract file and paste in `ClearTask.py`.
3. Compile/check the source before deployment.
4. Deploy with a concise title, an explicit rubric, and the worker's public wallet address.
5. Keep the client and worker wallets separate. The client funds escrow; the worker submits work; the client requests adjudication.
6. Confirm each write transaction has accepted consensus. A payout method queues an external GEN transfer for finalization; check that child transfer transaction before describing funds as received.

## Limits

This is an independently rebuilt demonstration contract, not a production audit. A subjective AI decision can still fail to reach validator consensus. No unilateral payout occurs on an undecided result; the mutual-settlement path requires both parties to sign. External payouts are asynchronous and must be confirmed on the explorer. Deploy only to a development/test network until the contract has been reviewed and exercised there.

