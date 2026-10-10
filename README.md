# ClearTask — GenLayer milestone escrow

This repository is preparing a fresh, standalone ClearTask contract build.

## Current submission source

- Contract: [fresh-cleartask/ClearTask.py](fresh-cleartask/ClearTask.py)
- Build, constructor, method, and deployment guide: [fresh-cleartask/README.md](fresh-cleartask/README.md)
- Review pull request: [#1](https://github.com/paulcaeser126/cleartask-genlayer/pull/1)

This contract uses a concrete pinned runner hash in its first-line `Depends` header. It has one revision, one appeal within a 15-minute window, and explicit client/worker permissions for escrow and settlement.

## Constructor

The deploying wallet becomes the client. Provide:

| Field | Type | Requirement |
|---|---|---|
| `title` | `str` | Required; at most 120 characters |
| `rubric` | `str` | Required; at most 4,000 characters |
| `worker_address` | `str` | The worker's 20-byte `0x` address |

Suggested demo title: `ClearTask GenLayer Contract Demo`.

## Public methods

`get_state`, `fund_escrow`, `submit_work`, `revise_submission`, `adjudicate`, `appeal`, `claim_payout`, `claim_refund`, `cancel`, `propose_mutual_settlement`, and `accept_mutual_settlement`.

Their arguments, caller requirements, and workflow are documented in [fresh-cleartask/README.md](fresh-cleartask/README.md).

## Validation and deployment status

GenVM lint passed, and schema validation recognized the constructor and all 11 public methods. The fresh build has **not yet been deployed** on Studio Next Dev. No address or successful transaction is claimed for this version.

The Studio session currently available to Codex is connected to a different wallet with less than 0.001 GEN; the Chrome wallet session is unavailable here. Deployment and a successful contract-call receipt still need to be completed with the funded client wallet.

## Evaluation checklist

- C1 — Public source repository: source is available in the repository branch and pull request linked above.
- C2 — README covers purpose, deployment, constructor inputs, and public methods: documented in [fresh-cleartask/README.md](fresh-cleartask/README.md).
- C3 — Successful Studio Next Dev deployment and address: pending.
- C4 — Successful contract-call transaction details: pending.

Files under `outputs/` and `docs/` describe earlier contract work and are retained as historical material; they are not part of this fresh build.
