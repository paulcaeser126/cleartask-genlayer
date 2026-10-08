# GenLayer Docs — retained working reference

Source: https://docs.genlayer.com/  
Bulk export: https://docs.genlayer.com/full-documentation.txt  
Reviewed: 2026-10-07

This is a compact working reference, not a copy of every API entry. The live official docs and bulk export are authoritative for exact signatures, network parameters, version requirements, and current behavior. I can use this reference in the current conversation; for future conversations, keep this file available or share the docs link again.

## Core mental model

GenLayer is an EVM-compatible chain paired with GenVM, a WebAssembly sandbox for Python Intelligent Contracts. It is aimed at applications whose shared outcome depends on interpreting natural language, unstructured evidence, live web content, or other non-deterministic inputs.

- **GenLayer Chain** orders EVM transactions and keeps authoritative consensus state: assignments, proposals, commits/reveals, decisions, appeals, fees, staking, and final outcomes.
- **Validator nodes** watch chain events, execute assigned work, maintain derived Intelligent Contract state, and submit consensus actions as EVM transactions. Outcome consensus is coordinated by chain contracts, not a separate validator P2P consensus network.
- **GenVM** executes Intelligent Contract code. Ordinary deterministic code must reproduce exactly; web/LLM work is isolated in non-deterministic blocks.
- Every Intelligent Contract has an EVM-facing **Ghost** at the same address on live networks. It routes calls/messages and holds the EVM-side native GEN balance. Studio does not fully reproduce Ghost/chain behavior.

## Transaction and consensus lifecycle

1. Caller submits an EVM transaction to the Intelligent Contract/Ghost; the consensus contracts queue it per recipient.
2. Protocol selects an activator, stake-weighted committee, and leader. Transactions for one contract are processed sequentially; different contracts can progress independently.
3. Leader executes in GenVM and proposes a receipt/state changes.
4. Committee independently follows execution. Deterministic portions must match; validators apply the contract’s equivalence rule to non-deterministic portions. Votes use commit then reveal.
5. Consensus can accept the proposal or produce timeout/undetermined outcomes; disagreement may rotate to another proposal round if funded budget permits.
6. Decision enters an appeal window. Validator appeals recheck relevant accepted/timeout decisions; leader appeals can restart execution for undetermined/leader-timeout outcomes. After appeals close, anyone may finalize.

**Accepted is not the same as successful or finalized.** Consensus can accept an error result as the correct execution outcome. EVM receipt/rollup settlement is also distinct from Intelligent Contract protocol finality. Apps should inspect GenLayer transaction status/lifecycle and wait for the state they require.

## Equivalence patterns

- `strict_eq`: all validator outputs must match exactly; canonicalize structured output (e.g. sort JSON keys). Good for objective, exact structured facts.
- `prompt_comparative`: leader and validators perform the task independently; a comparative prompt judges whether their outputs satisfy the developer’s principle. Good for complex outputs with a defined degree of similarity.
- `prompt_non_comparative`: leader produces an output; validators check it against source/input and explicit criteria without recreating the same answer. Good for summaries or open-ended outputs that can be judged for faithfulness.
- Custom leader/validator logic is available when built-ins do not express the desired tolerance or validation procedure.

Design the equivalence rule as part of the application’s security model. Define the question, eligible outcomes, authoritative evidence, criteria, deadline, and unavailable-evidence behavior. Return a compact structured result and apply consequences deterministically.

## Developer practices and cautions

- Use GenLayer for shared, enforceable judgment under adversarial conditions; use a conventional smart contract for fully deterministic logic and a backend where shared verifiability is unnecessary.
- Separate deterministic state changes from web/LLM operations. Expect providers, pages, rendering, and model outputs to vary.
- Treat web pages and user-provided text as untrusted input; prompt injection is a documented security topic. Keep instructions and evidence roles distinct, constrain outputs, and validate claims against sources.
- Store only necessary compact outputs; large source documents and reasoning increase cost and can raise privacy/reproducibility concerns.
- Internal message default is `on='finalized'`. `on='accepted'` can run before appeal closes, can repeat on re-execution, and cannot be rolled back if an appeal changes the outcome. Consumers must be idempotent. External messages are finalized-only.
- Never put private keys/recovery phrases in contract code, frontend source, logs, or examples.
- Studio is for development/testing, not a perfect live-network replica. Its EVM gas layer is gasless/compatibility-oriented while protocol fees may still apply; EVM contract execution, Ghost behavior, chain-layer behavior, and production web-access details may differ. Validate important behavior on the target network.
- Docs include development and validator setup guidance; versions, networks, RPCs, fees, stake parameters, and CLI behavior can change. Verify against current docs before relying on values.

## Documentation map

### Protocol
- Welcome / Get Started; Community and Careers.
- Discover the Protocol: What is GenLayer, How GenLayer Works, Use Cases, Core Concepts.
- Core Concepts: GenLayer Chain integration; Accounts and Addresses; GenVM; Non-deterministic Operations; LLM Integration; Web Data Access; Validators and Roles; Optimistic Democracy; Equivalence Principle; Appeals; Deterministic Violations & Tribunals; Protocol Randomness; Finality; Staking; Slashing; Unstaking; Transactions (types, execution, statuses, encoding/signing); Economic Model.

### Build on GenLayer
- Networks & RPCs; Consensus v0.6 Migration.
- Intelligent Contracts: Introduction, When to Use, Feature List (storage, errors, upgradability, value transfers, transaction context, messages, IC/EVM interactions, special methods, vector storage, debugging, randomness, non-determinism, LLMs, images, web access, balances), Development Setup, First Contract, types, storage, equivalence, Studio debugging, testing, deployment methods/config/CLI/scripts, prompt and data techniques, security/prompt injection, examples, CLI/Studio tools, ideas.
- Frontend & SDK Integration: architecture, DApp workflow, GenLayer JS, transaction queries, reading/writing data, fee policy/kit/profiling/outcomes, developer NFT rewards, Studio testing, boilerplate.
- Error & Revert Reference; Staking Contract Guide.

### Run a validator
- Setup Guide; Monitoring & Telemetry; Network Keeper Roles; System Requirements; GenVM Configuration; Upgrade Guide; Changelog.

### API references
- GenLayer CLI: environment, contract, transaction, configuration, network, account, staking, localnet and finalize commands.
- GenLayerJS: contracts, transactions, staking.
- GenLayerPY API; GenLayer Test (integration, direct mode, glsim).
- GenLayer Node API: GenLayer methods (`gen_call`, contract schema/state/code, receipt/status/lifecycle, syncing), debug, and ops methods.
- GenVM Linter and GenVM SDK.
- FAQ, Skills, and downloadable full documentation.

## Retrieval notes for follow-up questions

For code/API questions, first identify the relevant docs section and check the current page/export for exact syntax. For consensus questions, distinguish execution result, consensus decision, appeal status, and finality. For live deployment questions, identify network and documentation version before using addresses, RPCs, fee values, CLI commands, or validator parameters.
