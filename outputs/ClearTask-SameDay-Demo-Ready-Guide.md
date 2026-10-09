# ClearTask — current Studio Dev guide

## Active deployment

- Contract: https://explorer-studio-dev.genlayer.com/address/0x4D5887428F6C1aBD8D8ba880D59C3a342E07Bf8a
- Deployment transaction: https://explorer-studio-dev.genlayer.com/tx/0xdf3f193f676d96a2231a66e3e7e1a5e82ddcb22068b06516e8a016d567e870da
- Network: GenLayer Studio Next Dev, chain ID 61997
- Client: 0x70d086988706e4f27A53c484570B2a41897dcDD5
- Worker: 0x314C92977Ebc38Afe69dc643eF670cF4Dc5cAE17
- Current Explorer balance: 15 GEN

The Deploy transaction is FINALIZED, GenVM SUCCESS, and consensus Accepted. The successful fund_escrow transaction is 0x26418747a91f98268544b7b48745f62888b7821765c9a97b434b7d64a0795c56.

## What happened

A worker submit_work call finalized successfully as transaction 0x908fb2d3dc0b776f29af63c87583ee18898e71f842dcc8cdcaf96ff7b3525f01. It stored placeholder submission text. The later submit_work transaction 0x67e7b4075b728468f1a7f300fc60581a9f139d9b97c9a897510bb2a667b6c2f2 failed with “This milestone is not open for submissions” because the contract was already SUBMITTED. This is the expected guard for a second initial submission, not a reason to call submit_work again.

## Correct the stored submission

1. In Studio Next, call the read method get_state() and confirm status is SUBMITTED and revision_used is 0.
2. Sign in with the designated worker wallet 0x314C92977Ebc38Afe69dc643eF670cF4Dc5cAE17.
3. Expand revise_submission, not submit_work.
4. Enter the final work summary in submission and factual supporting details in evidence.
5. Send the transaction and verify Explorer shows Type Call, FINALIZED, GenVM SUCCESS, and Accepted consensus. Record its full hash and confirm get_state() shows the corrected values and revision_used=1.
6. Do not adjudicate until the source repository is public and the revision is verified.

The reviewer rubric treats links, filenames, and transaction hashes as references; reproduce short source/README excerpts and transaction result details in evidence. Do not claim a verdict, settlement, payout, or repository public access until verified.

## Constructor values

Title: ClearTask GenLayer Contract Demo

Rubric:
C1: The worker provides a public repository containing the standalone GenLayer contract source. | C2: The README explains the contract purpose, deployment steps, constructor inputs, and public methods. | C3: The contract successfully deploys in GenLayer Studio Next Dev, and its address is provided. | C4: The worker provides transaction details showing at least one successful contract call.

## Source

Use outputs/ClearTask-SameDay-Demo-Ready.py. It begins with the pinned Studio RC7 runner and is the standalone source for this deployment. Its public methods are documented in the root README.

## Wallet roles and safety

The deployment wallet is the immutable client and may fund or cancel before submission. Only the fixed designated worker may submit or revise work. The 15 GEN balance is Studio Dev test escrow and remains held while no decision is recorded. Do not use production funds.

