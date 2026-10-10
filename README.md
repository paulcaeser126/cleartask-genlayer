# ClearTask — GenLayer milestone escrow demo

ClearTask is a single-milestone GEN escrow contract. The client defines a title and numbered rubric, funds escrow, and a designated worker submits work and evidence. GenLayer validators assess the submitted text; the contract records a verdict and manages review, appeal, settlement, and payout states.

## Active Studio Next Dev instance

This is the current evaluation instance, deployed on GenLayer Studio Next Dev (chain ID 61997):

- Contract: [0x4D5887428F6C1aBD8D8ba880D59C3a342E07Bf8a](https://explorer-studio-dev.genlayer.com/address/0x4D5887428F6C1aBD8D8ba880D59C3a342E07Bf8a)
- Deployment transaction: [0xdf3f193f676d96a2231a66e3e7e1a5e82ddcb22068b06516e8a016d567e870da](https://explorer-studio-dev.genlayer.com/tx/0xdf3f193f676d96a2231a66e3e7e1a5e82ddcb22068b06516e8a016d567e870da)
- Client/deployer: 0x70d086988706e4f27A53c484570B2a41897dcDD5
- Designated worker: 0x314C92977Ebc38Afe69dc643eF670cF4Dc5cAE17
- Escrow funding: [0x26418747a91f98268544b7b48745f62888b7821765c9a97b434b7d64a0795c56](https://explorer-studio-dev.genlayer.com/tx/0x26418747a91f98268544b7b48745f62888b7821765c9a97b434b7d64a0795c56), finalized with GenVM SUCCESS and Accepted consensus; Explorer currently reports 15 GEN balance.

The deployment transaction is FINALIZED with GenVM SUCCESS and Accepted consensus. The Explorer Code tab matches the pinned-runner source published in outputs/ClearTask-SameDay-Demo-Ready.py.

### Current lifecycle status

The contract holds 15 GEN. The first worker call, [0x908fb2d3dc0b776f29af63c87583ee18898e71f842dcc8cdcaf96ff7b3525f01](https://explorer-studio-dev.genlayer.com/tx/0x908fb2d3dc0b776f29af63c87583ee18898e71f842dcc8cdcaf96ff7b3525f01), stored placeholder submission text. The designated worker then used revise_submission to replace it; the Studio state snapshot showed revision_used: 1 and the corrected text. Do not call submit_work again.

The client called adjudicate. Transaction [0x82f65c866722ad0eb95f589df42c14be5a23184decb39f7b8b773c12d42b9672](https://explorer-studio-dev.genlayer.com/tx/0x82f65c866722ad0eb95f589df42c14be5a23184decb39f7b8b773c12d42b9672) is FINALIZED with Result SUCCESS. This verifies a successful contract call. Read get_state() again in Studio to confirm the recorded status and verdict; the transaction result alone does not establish which verdict was recorded. No settlement or payout is claimed.
## Constructor inputs

Title: ClearTask GenLayer Contract Demo

Rubric:

C1: The worker provides a public repository containing the standalone GenLayer contract source. | C2: The README explains the contract purpose, deployment steps, constructor inputs, and public methods. | C3: The contract successfully deploys in GenLayer Studio Next Dev, and its address is provided. | C4: The worker provides transaction details showing at least one successful contract call.

The contract requires a non-empty title of at most 120 characters and one to eight rubric criteria with consecutive IDs (C1, C2, ...), each followed by a description. The deployed worker address is fixed in the source.

## Source and deployment steps

The deployed source is outputs/ClearTask-SameDay-Demo-Ready.py. It pins the GenVM runner in its first-line Depends header. To reproduce the demo, import this standalone file in Studio Next Dev, connect the intended client wallet, deploy with the title and rubric above, and verify that the Deploy transaction is FINALIZED with GenVM SUCCESS and Accepted consensus. The deploying address becomes the client. The client calls fund_escrow() with a positive Studio Dev GEN amount; the fixed worker then calls submit_work() once. Verify each transaction in Explorer.

This repository is publicly accessible for rubric criterion C1. GitHub Settings confirmed the repository visibility is Public on 2026-10-10.

## Public methods

- get_state() — reads title, rubric, client, worker, lifecycle status, submission/evidence, deadlines, verdict, escrow, and payout state.
- fund_escrow() — payable; client only, positive value, once while OPEN.
- cancel() — client only, before work is submitted.
- submit_work(submission, evidence) — designated worker only, after funding, once.
- revise_submission(submission, evidence) — designated worker can replace the initial submission once while SUBMITTED.
- refresh_submission_after_timeout(submission, evidence) — designated worker can refresh unresolved work after the review deadline, subject to the revision cap.
- adjudicate() — validators assess the fixed rubric against submitted text.
- appeal(reason, additional_evidence) — one permitted appeal by the party allowed for the verdict.
- finalize_decision() — settles after the appeal window.
- claim_payout() — sends the claimable escrow to the recorded recipient and marks transfer pending.
- confirm_payout() — recipient attests receipt after checking the external child transaction; this is not an on-chain proof of EOA receipt.

## Limits

This is a Studio Dev demonstration, not a production-funds deployment. No settlement or payout transaction is claimed in this repository.

