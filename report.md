# **Intuition Audit Competition on Hats.finance** 


## Introduction to Hats.finance


Hats.finance builds autonomous security infrastructure for integration with major DeFi protocols to secure users' assets. 
It aims to be the decentralized choice for Web3 security, offering proactive security mechanisms like decentralized audit competitions and bug bounties. 
The protocol facilitates audit competitions to quickly secure smart contracts by having auditors compete, thereby reducing auditing costs and accelerating submissions. 
This aligns with their mission of fostering a robust, secure, and scalable Web3 ecosystem through decentralized security solutions​.

## About Hats Audit Competition


Hats Audit Competitions offer a unique and decentralized approach to enhancing the security of web3 projects. Leveraging the large collective expertise of hundreds of skilled auditors, these competitions foster a proactive bug hunting environment to fortify projects before their launch. Unlike traditional security assessments, Hats Audit Competitions operate on a time-based and results-driven model, ensuring that only successful auditors are rewarded for their contributions. This pay-for-results ethos not only allocates budgets more efficiently by paying exclusively for identified vulnerabilities but also retains funds if no issues are discovered. With a streamlined evaluation process, Hats prioritizes quality over quantity by rewarding the first submitter of a vulnerability, thus eliminating duplicate efforts and attracting top talent in web3 auditing. The process embodies Hats Finance's commitment to reducing fees, maintaining project control, and promoting high-quality security assessments, setting a new standard for decentralized security in the web3 space​​.

## Intuition Overview

A next-generation verifiable data protocol creating a trustful interaction layer for the internet.

## Competition Details


- Type: A public audit competition hosted by Intuition
- Duration: 2 weeks
- Maximum Reward: $34,960.28
- Submissions: 88
- Total Payout: $5,993.17 distributed among 18 participants.

## Scope of Audit

## Project overview

Intuition is an Ethereum-based attestation protocol harnessing the wisdom of the crowds to create an open knowledge and reputation graph. Our infrastructure makes it easy for applications and their users to capture, explore, and curate verifiable data. We’ve prioritized making developer integrations easy and have implemented incentive structures that prioritize ‘useful’ data and discourage spam.

In bringing this new data layer to the decentralized web, we’re opening the flood gates to countless new use cases that we believe will kick off a consumer application boom.

The Intuition Knowledge Graph will be recognized as an organic flywheel, where the more developers that implement it, the more valuable the data it houses becomes.

The Intuition Protocol differentiates itself through a novel architecture that decouples “statements” from “attestations”, allowing for many-to-one relationships between "identities" and "claims" and enabling the easy integration of game-theoretic incentive mechanics. To unlock this many-to-one capability, Intuition allows system participants to 'Stake' on Identities and Claims, which acts as signaling of a user's level of conviction about the relevance of an Identity, or their level of conviction that a Claim is True or False.

The on-chain component of Intuition serves as 1) a registry of all identities (Atoms), 2) a registry of the relationships between identities (in the format of Semantic Triples), and 3) a handler of the economic state of all identities/claims and the respective tokenomics.

The complete Intuition system involves heavy use of off-chain decentralized data solutions. Akin to NFT architecture, solely the URI of each identity is stored on-chain, and all other data describing identities is stored off-chain.

## Audit competition scope

The scope of the bug bounty program includes the core contracts of the Intuition protocol:

- main branch ([contracts](https://github.com/0xIntuition/intuition-contracts/tree/main/src), [readme](https://github.com/0xIntuition/intuition-contracts/blob/main/README.md))

### Intuition Core Contracts

- EthMultiVault.sol
- AtomWallet.sol

## Examples of what's in scope

- Being able to steal assets (ETH) from users
- Being able to freeze assets (ETH) or make them inaccessible by their owners
- Unauthorized addresses are able to pause (or unpause) the protocol
- Being able to perform any kind of attack which will as a consequence lead to a user not receiving amount of assets (ETH) owned to them when they are redeeming their shares
- Being able to perform any kind of attack that will lead to the internal accounting of the EthMultiVault contract not working as intended (over or undercharging for certain fees, inflation attacks, etc.)
- Being able to change EthMultiVault or AtomWallet configuration parameters without the admin’s or owner’s approval

## Intended behavior

Please refer to the [readme file](https://github.com/0xIntuition/intuition-contracts/blob/main/README.md) as well as our [developer docs](https://intuition.gitbook.io/intuition-contracts) for an extensive overview of the intended behavior of the smart contracts.




## Low severity issues


- **AtomWallet's `execute` Function Needs `payable` Keyword to Handle ETH Transfers**

  In `AtomWallet.execute()`, the function lacks the `payable` keyword, causing attempts to send Ether (ETH) to the function to fail. This results in the contract not operating as intended during ETH transfers, leading to failed transactions and potential financial loss when Ether is sent but not handled, getting stuck. Adding the `payable` keyword is recommended to resolve this issue and improve functionality.


  **Link**: [Issue #2](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/2)


- **Front-Running Vulnerability in EthMultiVault's depositTriple Allows Denial of Service**

  The `depositTriple()` function in the `EthMultiVault` contract can be exploited to cause a denial-of-service attack. A malicious actor can front-run a large deposit by sending a minimum deposit to the counter vault of the user's intended deposit, causing the user's transaction to fail.


  **Link**: [Issue #8](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/8)


- **Implement Deadline Check to Ensure Time Validity of UserOperations in AtomWallet**

  The `_validateSignature` function was noted for missing checks on `validUntil` and `validAfter` fields, essential for ensuring the execution time of transactions, preventing potential user losses. Although the current implementation doesn't rely on time-sensitive operations, adding these checks could improve transaction safety. Integrating such validation would enhance user protection against delayed transaction risks.


  **Link**: [Issue #25](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/25)


- **Potential Loss of Initial Deposit in Atom Wallets if `atomWarden` is Changed**

  When creating new Atom wallets, two steps occur: creation of the Atom vault and deploying the wallet. If the `atomWarden` changes after the wallet address is precomputed but before it is deployed, the funds deposited to the initial address will become unclaimable. Deploying the wallet after a `atomWarden` change will result in a different address, causing the initial deposit amount to be effectively lost. The suggested fix involves ensuring that all created Atom wallets are deployed before changing the `atomWarden` or keeping track of the mapping between vault IDs and wallet addresses on-chain.


  **Link**: [Issue #50](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/50)


- **Add Safety Check for `atomDepositFractionForTriple` to Prevent Unreasonable Values**

  There's a potential problem with the `setAtomDepositFractionForTriple()` function, where no check ensures that the value does not exceed `feeDenominator`. This oversight could cause transactions to revert due to an underflow error. It is recommended to add a requirement to prevent setting an unreasonable `atomDepositFractionForTriple` value.


  **Link**: [Issue #54](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/54)


- **Issue with executeBatch Function in AtomWallet Prevents Sending ETH**

  The `AtomWallet::executeBatch` function cannot send positive ETH values because it hardcodes the ETH value to zero in its inner `_call` function. This differs from `AtomWallet::execute`, which allows specifying ETH values, causing unfulfilled state changes. The suggested fix is to introduce an array parameter to specify ETH amounts for each transaction, aligning both functions' behaviors.


  **Link**: [Issue #61](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/61)

## Minor severity issues


- **Adopt Ownable2StepUpgradeable to Prevent Ownership Loss in AtomWallet.sol**

  In the contract `AtomWallet.sol`, ownership is transferred in a single step, which poses a risk if an incorrect address is provided. This could leave the contract without an owner, making it non-upgradable. The recommendation is to switch to a two-step transfer mechanism, such as `OZ::Ownable2StepUpgradeable.sol`, to enhance security.


  **Link**: [Issue #14](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/14)


- **Enhancement Suggestion: Add Events to `onlyAdmin` Functions Changing Critical Parameters**

  The `onlyAdmin` functions that alter critical parameters in `EthMultiVault.sol` should emit events to capture these changes. Emitting events enhances transparency, allowing off-chain tools to register changes, and helps users make informed decisions. Lack of such events may reduce protocol trustworthiness and could negatively affect liquidity and reputation. Therefore, it's recommended to add events to functions like `setProtocolVault()`, `setMinDeposit()`, and others.


  **Link**: [Issue #17](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/17)


- **Correct NatSpec Comment for `cancelOperation()` to Reflect Its Actual Functionality**

  The `cancelOperation()` function's NatSpec comment is inaccurate and misleading, suggesting it executes rather than cancels an operation. Correcting the comment to reflect its true functionality is essential for code clarity and comprehension. This improvement is classified as an enhancement for accurate documentation, which benefits future developers and auditors.


  **Link**: [Issue #22](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/22)


- **Lack of Validation Allows Atom Wallets to Be Created for Triple Vaults**

  There's an insufficient validation in the deployment function for Atom wallets, allowing wallets to be incorrectly deployed for Triple vaults. This occurs due to the lack of checks to ensure that the provided ID isn’t associated with a Triple vault. This functionality is intentional but could benefit from an additional validation step to prevent misuse.


  **Link**: [Issue #32](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/32)


- **Fixing Zero `atomDepositFraction` to Prevent Triple Vault Deposit Reverts**

  When making a new deposit to Triple Vaults, a part of the share goes to linked Atoms Vaults. The current protocol doesn’t check if this value is zero, causing transaction failures. The proposed solution is to skip depositing if the atomDepositFraction is zero to improve code robustness.


  **Link**: [Issue #49](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/49)


- **_fix_createTriple Function to Align with Documentation on Triple Usage_**

  The logic of `_createTriple()` does not align with the documentation that states triples can be used as atoms in other triples. The function currently reverts if one of the IDs is a triple vault ID, contradicting the intended design. The documentation should clarify that users need to create new atoms with the triple’s claim as the `atomUri`.


  **Link**: [Issue #55](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/55)


- **Modify Atom Wallet Deployment Logic to Conform with ERC4337 Standard**

  When deploying AA Atom wallets using EthMultiVault (SingleTone), the transaction reverts if the result is `address(0)`, which can happen due to hash collision or if the contract is already deployed. According to ERC4337, the deployment process should return the AA address rather than reverting. This would facilitate easier querying and simulation for clients. The suggested solution is to return the existing address instead of reverting and provides code changes to implement this enhancement.


  **Link**: [Issue #57](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/57)


- **Add Storage Gap in AtomWallet.sol and EthMultiWallet.sol to Prevent Collisions**

  Both `AtomWallet.sol` and `EthMultiWallet.sol` lack the necessary storage gaps for upgradeability via the Proxy pattern. Without these gaps, adding new variables in future upgrades could lead to storage collisions, potentially causing serious issues. To prevent this, it is recommended to introduce storage gaps as per OpenZeppelin guidelines.


  **Link**: [Issue #60](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/60)


- **Ensure Max Redemption Check in `redeemAtom` and `redeemTriple` Functions for EIP-4626 Compliance**

  The `redeemAtom` and `redeemTriple` functions for Atom and Triple vaults do not verify the user's maximum redeemable shares, potentially leading to over-redemption. Though the `_redeem` function indirectly checks share balance, explicitly incorporating `maxRedeem` can ensure clearer compliance with the ERC-4626 standard.


  **Link**: [Issue #65](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/65)


- **Discrepancy in EthMultiVault Redeemed Event Emission May Mislead Off-Chain Systems**

  In the `_redeem` function of the EthMultiVault contract, there's a discrepancy where the `Redeemed` event emits the owner's balance instead of the total vault balance as intended. This inconsistency can confuse off-chain systems and dApps, potentially leading to a misrepresentation of the vault's state and incorrect decision-making by users or automated systems.


  **Link**: [Issue #71](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/71)


- **Minor inefficiencies in EthMultiVault's `_deposit` function affect gas usage and clarity**

  In the `_deposit` function of the EthMultiVault contract, there are two inefficiencies: a redundant comparison of `totalAssetsDelta` with zero and an unnecessary variable `totalSharesDelta` that's identical to `sharesForReceiver`. Addressing these issues could optimize gas usage and improve code clarity, although they don't affect security.


  **Link**: [Issue #72](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/72)


- **Inconsistent Signature Validation in AtomWallet Implementation Against ERC-4337 Standard**

  The AtomWallet implementation does not check the error return value of the `ECDSA.tryRecover` function, causing the `_validateSignature` function to not revert as specified by ERC-4337. Updated code proposals include more robust error handling and adherence to the ERC-4337 standards, ensuring proper signature validation and error management.


  **Link**: [Issue #86](https://github.com/hats-finance/Intuition-0x538dbadc50cc87b281cd655f1edbc6ebda02a66a/issues/86)



## Conclusion

The audit competition for Intuition on Hats.finance revealed several issues across various severities, underscoring the advantage of decentralized security mechanisms. Despite the robust architecture of Intuition—an Ethereum-based attestation protocol aimed at creating an open knowledge and reputation graph—specific vulnerabilities were detected in core contracts like EthMultiVault.sol and AtomWallet.sol. These included potential asset theft and denial-of-service attacks, stemming from missing `payable` keywords, inadequate front-running protections, and improper economic validations. Additional minor and low-severity issues ranged from missing event emissions and documentation inaccuracies to efficiency concerns and suboptimal signature validations. The audit competition effectively leveraged a large pool of skilled auditors, resulting in a relatively low payout ($5,993.17) compared to the potential maximum ($34,960.28), indicating a moderate level of vulnerability. This audit not only highlighted critical areas for improvement but also reinforced Hats.finance’s commitment to fostering a secure and scalable Web3 ecosystem through decentralized audit competitions.

## Disclaimer


This report does not assert that the audited contracts are completely secure. Continuous review and comprehensive testing are advised before deploying critical smart contracts.


The Intuition audit competition illustrates the collaborative effort in identifying and rectifying potential vulnerabilities, enhancing the overall security and functionality of the platform.


Hats.finance does not provide any guarantee or warranty regarding the security of this project. Smart contract software should be used at the sole risk and responsibility of users.

