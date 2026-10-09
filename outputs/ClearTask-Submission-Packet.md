# ClearTask submission packet — draft pending two user steps

Do not paste or send this packet until (1) GitHub shows the repository is public and (2) the worker has signed the correction with revise_submission. The existing on-chain submission contains placeholders.

## Submission field

ClearTask is deployed on GenLayer Studio Next Dev (chain ID 61997) at 0x4D5887428F6C1aBD8D8ba880D59C3a342E07Bf8a. Source and README: https://github.com/paulcaeser126/cleartask-genlayer. The deployment transaction 0xdf3f193f676d96a2231a66e3e7e1a5e82ddcb22068b06516e8a016d567e870da finalized with GenVM SUCCESS and Accepted consensus. The client is 0x70d086988706e4f27A53c484570B2a41897dcDD5; the designated worker is 0x314C92977Ebc38Afe69dc643eF670cF4Dc5cAE17.

## Evidence field

Source excerpt from outputs/ClearTask-SameDay-Demo-Ready.py: the file pins py-genlayer:5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng and defines ClearTask as a GenLayer contract. It exposes get_state, fund_escrow, submit_work, revise_submission, refresh_submission_after_timeout, adjudicate, appeal, finalize_decision, claim_payout, and confirm_payout.

README excerpt: ClearTask is a single-milestone GEN escrow contract. The client defines a title and numbered rubric, funds escrow, and a designated worker submits work and evidence. The README documents constructor values, deployment steps, and all public methods.

Deployment proof: 0x4D5887428F6C1aBD8D8ba880D59C3a342E07Bf8a was created by 0x70d086988706e4f27A53c484570B2a41897dcDD5. Deploy transaction 0xdf3f193f676d96a2231a66e3e7e1a5e82ddcb22068b06516e8a016d567e870da: FINALIZED, GenVM SUCCESS, consensus Accepted. Funding transaction 0x26418747a91f98268544b7b48745f62888b7821765c9a97b434b7d64a0795c56: FINALIZED, GenVM SUCCESS, consensus Accepted; Explorer reports 15 GEN balance.

Successful contract-call proof: worker call 0x908fb2d3dc0b776f29af63c87583ee18898e71f842dcc8cdcaf96ff7b3525f01 was Type Call, FINALIZED, from 0x314C92977Ebc38Afe69dc643eF670cF4Dc5cAE17 to the contract, GenVM SUCCESS, consensus Accepted. That call initially stored placeholder text; a second submit_work attempt correctly failed because the milestone was already SUBMITTED. After the worker signs revise_submission, add the revision transaction hash and its FINALIZED/SUCCESS/Accepted result here. No adjudication or payout is claimed.

