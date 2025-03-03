# Context
## Challenges
As well as preserving the confidentiality of user data, the other main goals of this protocol are to be fully permissionless and decentralized and to be a generic smart-contract execution engine compatible to the greatest extent with the EVM. To achieve this, Obscuro pioneers a novel consensus protocol.

Relying on hardware-based TEEs for applications where significant value depends on the security of the hardware poses several challenges. A system designed to manage value should not allow an attacker capable of compromising secure hardware to take ownership of the value under any circumstances. In other words, the ledger's integrity should not depend on TEEs being 100% hack-proof. Obscuro uses the security of Ethereum combined with game theory to detect and correct eventual TEE hacks.

A system where everything is encrypted all the time is not usable. There must be a way for users to query their data or prove it to third parties. Additionally, an existing application contract which reveals internal state (such as the balanceOf(address) function of the ERC-20 standard used to look up anyone's holding) need to be considered carefully; since while Obscuro would prevent the state of the contract being visible, the functions might not.

Another critical challenge for this protocol is the prevention of MEV. Because user transactions and execution are not visible to the aggregators, one might naively assume that this problem is solved. Unfortunately, that is not strictly true since aggregators might gain useful information through side-channels and use that to extract value. For example, an aggregator might own some accounts and submit transactions to them in key moments and then query for results. Obscuro introduces some novel techniques to prevent aggregators from performing replay-attacks which can generally be used for side-channels.

A privacy-preserving platform should consider illegal usage and design to prevent it.  The knowledge that transactions will become public in the future is a key element of the game-theoretic analysis. It is a deterrent for users to engage in criminal behavior, like money laundering, because law-enforcement agencies will eventually catch up. An important insight is that the value of confidentiality decays over time, to the point where transactions may just be of historical interest. For most transactions involving value, it is critical that they are not public when processed and cannot be front-run. For other transactions, it is critical that they are not visible for a period after they occur, such that competitors cannot take advantage of that information. Obscuro uses this insight and implements a policy for delayed transactions revealing. While it provides confidentiality when it matters most, thus providing value for its users, it also allows for the system's activity to be retrospectively analyzed once the time-criticality of transactions has passed. [Alternative](./appendix#alternative-revelation-options) options have been considered.

One important challenge of such a system is making sure that some catastrophic event cannot leave all the value locked.

High transaction fees is one of the main barriers of entry for Ethereum. Obscuro addresses this using a novel approach to calculate fees based on the actual costs of running nodes.

## Design Principles
The solution follows the following principles:
* Complexity makes the solution harder to reason about and increases the risk of successful attacks, so the solution is as simple as possible and as widely applicable as possible.
* Embrace the characteristics of the underlying L1 blockchain and use them to create a simple model. The L1 blockchain should not require change.
* Prioritise immediate funds access for business-as-usual processing over TEE compromise. The use of TEEs allows a solution with multiple advantages over optimistic L2 solutions, which cannot grant immediate access for funds to the same degree.
* Use game theory and incentives to reinforce correct behavior and penalize subversive, disruptive actions.
* Structure the solution so that other L1 blockchains or other confidential computing techniques could be adopted.
* Make the software (contracts, TEE environments) open-source to support the permissionless goals and permit validation of the correct operation of the L2 network. Additionally, deployed applications should remain auditable.

