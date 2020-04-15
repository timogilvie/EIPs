---
eip: <to be assigned>
title: Fungible Staking Pool Standard
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
A number of Proof-of-Stake blockchains and scaling solutions have launched tokens on Ethereum. To stake in one of these networks, tokens are forwarded to a smart contract which handles staking logic. However, when the tokens are staked, and locked in a staking smart contract, tokens lose their fungiblity and the network effects of following a widely adopted standard. This EIP sets a standard for fungible staking pools which wrap tokens in a staking smart contract.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
The primary challenge in designing a standard fungible staking pool is that Proof-of-stake networks often have a rewards delay and an unbonding period. The rewards delay causes deposits to be unique until they are past the rewards delay (i.e. dependent on the deposit time), and the unbonding period causes the same problem at the end of the lifecycle. 

***Option A***  
To address this challenge, we accept there is or may be a rewards delay, but don't differentiate whether an underlying token is earning rewards or not. New deposits will cause the pool's yield to drop because the underlying tokens aren't earning rewards until they have passed the rewards delay. Therefore all deposits are an ERC-20 representing the fungible staking pool.

However, to unbond, we can optionally issue an ERC-721 to represent when someone has sent in their ERC-20 tokens to be unbonded.

*Pros*: Easier to implement. Simpler to interact with. Better user experience from minting perspective.  
*Cons*: Lower yield for ERC-20 representations of the fungible staking pool.

***Option B***  
Here, our standard describes an optional ERC-721 extension for staked tokens under the rewards delay, a required ERC-20 extension for staked tokens earning rewards (fungible staking pool), and an optional ERC-721 extension for tokens in the unbonding period.

*Pros*: Maximizes yield for the ERC-20 representation of the fungible staking pool.  
*Cons*: Three different token representations. Extra work for integrations, wallets, developers.

## Motivation
<!--The motivation is critical for EIPs that want to change the Ethereum protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the EIP solves. EIP submissions without sufficient motivation may be rejected outright.-->
The fungible representation of a staked token allows for it to earn yield and be transferable. This allows the fungible representation to be used in more sophisticed applications or dApps. For example, a fungible staked token can accumulate interest while also sitting in an automated market maker or lending protocol. Importantly, establishing this fungible representation in a standard EIP increases network effects across the ecosystem; rather than supporting numerous implementations developers and wallets can interact with one standard and ensure forwards and backward compability.

## Specification - Option A
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Ethereum platforms (go-ethereum, parity, cpp-ethereum, ethereumj, ethereumjs, and [others](https://github.com/ethereum/wiki/wiki/Clients)).-->

**MUST Follow [ERC-20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md), In Addition to Methods Below**

#### validator

Returns the name of the validator - e.g. `"Staked"`.

OPTIONAL - This method can be used to improve usability,
but interfaces and other contracts MUST NOT expect these values to be present.


``` js
function validator() public view returns (string)
```


#### validatorFee - UINT??

Returns the validator fee, as a percent of the staking rewards (yield earned) - e.g. `.10` to represent a 10% of rewards.

OPTIONAL - This method can be used to improve usability,
but interfaces and other contracts MUST NOT expect these values to be present.

``` js
function validatorFee() public view returns (uint)
``` 



#### yield - UINT??

Returns the current staking yield - e.g. `.10` to represent a 10% staking yield.

***Notes*** 
- Sometimes this will be an expected yield, sometimes it may be calculable directly. How do we want to approach this? We could use historical yield, sort of how uniswap v2 is using volume weighted price over time?

OPTIONAL - This method can be used to improve usability,
but interfaces and other contracts MUST NOT expect these values to be present.

``` js
function yield() public view returns (uint)
```



#### exchangeRate

Returns the exchange rate of the ERC-20 staked representation to the underlying token - e.g. `10` means the ERC-20 represents 10 of the underlying token.

OPTIONAL - This method can be used to improve usability,
but interfaces and other contracts MUST NOT expect these values to be present.

``` js
function exchangeRate() public view returns (uint)
```



#### rewardsDelay - Is this fixed?? Do we want it?

Returns the number of blocks before a staked token earns rewards - e.g. `10`.

***Notes*** 
- If this isn't fixed it doesn't make sense

OPTIONAL - This method can be used to improve usability,
but interfaces and other contracts MUST NOT expect these values to be present.

``` js
function rewardsDelay() public view returns (uint8)
```



#### unbondingPeriod

Returns the unbonding period of the staked token, in blocks. E.g. `100`.

OPTIONAL - This method can be used to improve usability,
but interfaces and other contracts MUST NOT expect these values to be present.

``` js
function unbondingPeriod() public view returns (uint)
```



#### unbondBalanceOf

Returns the unbonding information of an account with address `_owner`. Do we want to use timestamp here or block numbers for the tuple return value?

``` js
function unbondBalanceOf(address owner) public view returns (uint balance, uint initBlock, uint unbondedBlock)
```



#### mint

Transfers `_value` amount of tokens from msg.sender to itself (contract), stakes the tokens transferred, and MUST fire the `MINT` event.

The function SHOULD `throw` if the message caller's account balance does not have enough tokens to spend.

*Note* Mints of 0 values MUST be treated as normal mints and fire the `MINT` event.

``` js
function mint(uint value) public returns (bool success)
```


#### claimRewardsAndRestake

Claims rewards for the pool and restakes them, and MUST fire the `claimRewardsAndRestake` event.

OPTIONAL - This method is used to compound rewards on networks where it is required.


``` js
function claimRewardsAndRestake() public returns (bool success)
```



#### unbond

Begins the unbonding process for `amount` number of tokens, and MUST fire the `Unbond` event. This method decrements the msg.sender's balanceOf, and updates their unbondBalanceOf with the proper information.


``` js
function unbond(uint amount) public returns (bool success)
```



#### claimUnderlying

Attempts to withdraw the underlying token if the unbondBalanceOf amount is greater than the claim amount, and if the block is greater than or equal to the specified unbondedBlock.


``` js
function claimUnderlying(uint amount) public returns (bool success)
```



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
