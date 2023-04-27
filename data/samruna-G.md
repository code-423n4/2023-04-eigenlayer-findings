GAS Optimization
1. Use of custom errors
Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Proposed solution:
change require(msg.sender != owner, "Unauthorized")
to 
if (msg.sender != owner)
            revert Unauthorized();

Code references where this can be changed
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/DelegationManager.sol#L71
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/DelegationManager.sol#L97
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/DelegationManager.sol#L121-125
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/DelegationManager.sol#L137
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/DelegationManager.sol#L305-315
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L105
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L116
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L152
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L171
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L181
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L187
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L204
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L385
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L415
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L513
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/Slasher.sol#L537
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L262
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L290-295
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L342-343
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L360-365
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L495
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L542-548
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L631-636
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L484-490
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L750-766

This is applicable to every contract in scope, so not listing all them here.

2. Correct operator to check unit value
for uint validation, use != iinstead of >0. this saves gas-efficient

3. Loop index increments
The loop index increments can be written as unchecked { ++i } instead of simply i++ to save gas.
Two reasons:
unchecked because the loop variable i is of type uint256, therefore we would run out of gas before triggering an overflow
++i instead of i++ because it is slightly more efficient
++i is cheaper than i++

Code references:
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L466
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BN254.sol#L172
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BN254.sol#L223
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L137
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L145
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L115

4. Unchecked block in loops
Use of unchecked block in loops for arithmatic ops - to avoid overflow checks
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

for(uint256 i; i < 10; i++){
//doSomething
}

can be written as shown below.

for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}

Code references:
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L466
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BN254.sol#L172
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BN254.sol#L223
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L137
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L145
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L115

5. Packed vs unpacked structure
In ethereum, you pay gas for every storage slot you use. A slot is of 256 bits, and you can pack as many variables as you want in it. Packing is done by solidity compiler and optimizer automatically, you just need to declare the packable functions consecutively.

The below code is an example of poor code and will consume 3 storage slot

uint8 numberOne;
uint256 bigNumber;
uint8 numberTwo;
A much more efficient way to do this in solidity will be

uint8 numberOne;
uint8 numberTwo;
uint256 bigNumber;
This small change will save you a lot of gas as it will now only need 2 slots to store (It takes 20k gas to store 1 slot of data). These small things are what make solidity different from other languages. Stuff like the order of variables never mattered as much in other languages when it came to optimization. Another thing to notice is that structs, mappings, and arrays always start from a new slot.

code references:
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/IDelayedWithdrawalRouter.sol#L6
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/IPaymentManager.sol#L55
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/IStrategyManager.sol#L26

