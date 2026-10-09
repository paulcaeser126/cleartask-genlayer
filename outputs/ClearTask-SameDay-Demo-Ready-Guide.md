# ClearTask SameDay Demo — deployment and completion guide

## Verified deployment

- **Contract:** [0xB9B11468637d3aF6685A0adeAA640487Fa9BCC2B](https://explorer-studio-next.genlayer.com/address/0xB9B11468637d3aF6685A0adeAA640487Fa9BCC2B)
- **Deployment transaction:** [0x6c0c380cc921326f90515acb427b76ef1a522481e7c8deafab8914c0bc04f7a8](https://explorer-studio-next.genlayer.com/tx/0x6c0c380cc921326f90515acb427b76ef1a522481e7c8deafab8914c0bc04f7a8)
- **Status:** `FINALIZED`; consensus `Accepted`
- **Deployer/client on this instance:** `0xB4E3e2D252Ce070e2289c62c9A09265b296FB782`
- **Client wallet selected for this demo:** `0xB4E3e2D252Ce070e2289c62c9A09265b296FB782`
- **Designated worker:** `0x314C92977Ebc38Afe69dc643eF670cF4Dc5cAE17`
- **Network:** GenLayer Studio Next Dev, chain ID `61997`

The source embedded in the deployment transaction is the corrected ready source. It fixes the rubric requirement and restricts `submit_work` to the designated worker address. The contract stores its deployer as `client`; the owner chose to use this existing `0xB4…B782` client, so no redeployment is needed. A live `get_state()` read confirms the milestone is `OPEN` with zero escrow and the designated worker recorded. Fund it only with Studio Dev test funds from the client wallet.

## Constructor inputs used

**title**

```text
ClearTask GenLayer Contract Demo
```

**rubric**

```text
C1: The worker provides a public repository containing the standalone GenLayer contract source. | C2: The README explains the contract purpose, deployment steps, constructor inputs, and public methods. | C3: The contract successfully deploys in GenLayer Studio Next Dev, and its address is provided. | C4: The worker provides transaction details showing at least one successful contract call.
```

Use consecutive rubric IDs (`C1`, `C2`, and so on), with a description after each colon.

## Remaining submission items

1. **Make the repository public and verify access.** The owner approved public visibility, but GitHub requested password reauthentication. After completing it, open the repository in a signed-out or private browser window to confirm reviewers can access it.
2. **Fund the demo escrow.** From client wallet `0xB4…B782`, send a positive Studio Dev GEN amount to `fund_escrow()`. The milestone currently has zero escrow. Use test funds only.
3. **Complete a worker contract call.** After funding, have `0x314C…AE17` call `submit_work` with non-empty submission and evidence. Confirm its Explorer transaction shows `Call`, `FINALIZED`, GenVM `SUCCESS`, and consensus `Accepted`.
4. **Provide the final evidence.** Include the public repository link, deployed contract address, deployment transaction link, and successful worker-call transaction link.

The previous contract at `0xDba641391485A7697E22ef794F488e256c3f3DA6` is separate and still holds the previously reported 15 GEN Studio Dev escrow. Nothing in this deployment moved or released that escrow. Keep this workflow on Studio Dev test funds; production payout behavior has not been verified.

