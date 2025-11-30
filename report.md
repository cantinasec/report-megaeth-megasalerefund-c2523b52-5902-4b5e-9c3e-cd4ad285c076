## Gas Optimization
### `batchRefund()` logic can be simplified
<!--
Number: 2
Cantina code repository status: new
Hyperlink: [Issue 2](https://cantina.xyz/code/c2523b52-5902-4b5e-9c3e-cd4ad285c076/findings/2)
Labels: [None]
Fixed on: [None]
-->


**Severity:** Gas Optimization

**Context:** _(No context files were provided by the reviewer)_


**Description:** The `batchRefund()` function emits an event `BatchRefundProcessed(uint256 usersCount, uint256 totalAmount);` and makes use of the following logic to track `usersCount` and `totalAmount` refunded in the batch:

```solidity
uint256 batchTotalAmount = 0;
uint256 successfulRefunds = 0; // for tracking user count

for (uint256 i = 0; i < usersCount; i++) {
    _processSingleRefund(users[i], amounts[i], merkleProofs[i]);
    batchTotalAmount += amounts[i];
    successfulRefunds++;
}
```

But this local variable `successfulRefunds` is not required. The `batchRefund` only ever executes in full, it succeeds only if all refunds succeed as per the input, and we already have the user count from the input `users` array.



**Recommendation:** Consider using the already known users count for event data, and removing the `successfulRefunds` variable.


<!--
POTENTIAL RESOLUTION STATEMENT:

**MegaETH:** new.


-->



## Informational
### Incorrect natspec
<!--
Number: 1
Cantina code repository status: new
Hyperlink: [Issue 1](https://cantina.xyz/code/c2523b52-5902-4b5e-9c3e-cd4ad285c076/findings/1)
Labels: [None]
Fixed on: [None]
-->


**Severity:** Informational

**Context:** [MegaSaleRefund.sol#L160](https://cantina.xyz/code/c2523b52-5902-4b5e-9c3e-cd4ad285c076/src/MegaSaleRefund.sol#L160)


**Description:** The natspec documentation above `_processSingleRefund()` function says:

```solidity
// PREREQUISITE: sale contract must grant TOKEN_RECOVERER_ROLE to this contract
// @dev This is checked in _processRefund() via sale.hasRole(sale.TOKEN_RECOVERER_ROLE(), address(this))
```

But this is an incorrect description. The mentioned check is actually done in `refund()` and `batchRefund()` functions, and `_processRefund()` function does not even exist.


**Recommendation:** Consider changing the natspec comments.


<!--
POTENTIAL RESOLUTION STATEMENT:

**MegaETH:** new.


-->



### Make sure that `TokenSale` entity addresses can handle refunded USDT
<!--
Number: 3
Cantina code repository status: new
Hyperlink: [Issue 3](https://cantina.xyz/code/c2523b52-5902-4b5e-9c3e-cd4ad285c076/findings/3)
Labels: [None]
Fixed on: [None]
-->


**Severity:** Informational

**Context:** _(No context files were provided by the reviewer)_


**Description:** The `TokenSale` contract provided a way for users to bid with USDT, and the `MegaSaleRefund` contract is aimed at providing 10% rebates for users who opted in to lock the $MEGA tokens for a year.

The `refund()` function here uses the input `to address` to check user entry in the merkle tree (list of all user entities that are eligible for refund), and also sends the refunded USDT back to this "to" address. The user does not have a way to receive the refunds at a different address.

This works fine if all entities that interacted with `TokenSale` contract were EOAs. But if they were contracts with hardcoded pathways, they might not be able to pull those USDT out after receiving the refund at their own contract.



**Recommendation:** Consider checking if user entities would have a problem handling received USDT refunds, if they used smart contracts for the public sale.


<!--
POTENTIAL RESOLUTION STATEMENT:

**MegaETH:** new.


-->

