---
eip: <to be assigned>
title: Fungible Staking Derivative
author: Tim Ogilvie @timogilvie, Sam Mitchell @samatstaked, Devan Purhar @dpurhar27
discussions-to:
status: Draft
type: Standards Track
category: ERC
created: 2020-4-13
requires: EIP-20
---

<!--You can leave these HTML comments in your merged EIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new EIPs. Note that an EIP number will be assigned by an editor. When opening a pull request to submit your EIP, please use an abbreviated title in the filename, `eip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the EIP.-->
A number of new-generation POS blockchains and scaling solutions have launched tokens on Ethereum. To stake in one of these networks, tokens are forwarded to a smart contract which handles the staking logic. When locked in a staking smart contract, tokens lose their fungiblity status and the network effects of following a widely adopted standard. This EIP sets a standard for fungible staking derivatives which wrap tokens in a staking smart contract.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Implementation details here. Is it an extension of ERC-20 or ERC-777?

## Motivation
<!--The motivation is critical for EIPs that want to change the Ethereum protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the EIP solves. EIP submissions without sufficient motivation may be rejected outright.-->
A fungible representation of staking balances allows for the easy transfer of staked positions and use in more sophisticated applications such as automated market makers (AMM), exchanges, and asset management protocols. Establishing this fungible representation in a standard increases network effects across the ecosystem; rather than supporting numerous implementations developers and wallets can interact with one standard and ensure forwards and backward compability.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Ethereum platforms (go-ethereum, parity, cpp-ethereum, ethereumj, ethereumjs, and [others](https://github.com/ethereum/wiki/wiki/Clients)).-->

### Interface:

`Mint (owner, address)`
-Deposit LPT (Or other eligible ERC-20) with a validator address. Staked by default.
-Stakes LPT by calling the staking functions. 
-Creates two underlying tokens that represent ownership: one is the staked LPT and the other receives tokens

`Transfer()`
`BalanceOf()`

`Change-Validator()`
-Change validator to new address
-Optional function

`Claim-Rewards()`
-Claims any outstanding rewards by calling the appropriate function
-Pays to the rewards token

`Sell-Rewards()`
-Use an AMM to convert rewards to stable coins 

`Burn()`
-Calls unstake.
-Waits unbonding period (or until activated by an external caller)
-Claims any outstanding rewards
-Withdraws funds and returns to withdraw address

### Views


## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

## Backwards Compatibility
<!--All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

## Test Cases
<!--Test cases for an implementation are mandatory for EIPs that are affecting consensus changes. Other EIPs can choose to include links to test cases if applicable.-->
Test cases for an implementation are mandatory for EIPs that are affecting consensus changes. Other EIPs can choose to include links to test cases if applicable.

## Implementation
<!--The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.

## Security Considerations
<!--All EIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. EIP submissions missing the "Security Considerations" section will be rejected. An EIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
All EIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. EIP submissions missing the "Security Considerations" section will be rejected. An EIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
