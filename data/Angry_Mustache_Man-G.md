# [G-01] require()/revert() strings longer than 32 bytes cost extra gas
Each extra chunk of bytes past the original 32 iincurs an MSTORE which costs 3 gas.

All the instances where require is used should replace its long error string with short strings.

# [G-02] Use nested if and, avoid multiple check combinations
Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

Instances:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L422

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L562

# [G-03] Shift the require statement to save gas 

Shift the line :
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L147

to

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L141

to save gas. 

# [G-04] Remove the initializer modifier
If we can just ensure that the initialize() function could only be called from within the constructor, we shouldn’t need to worry about it getting called again.
In the EVM, the constructor’s job is actually to return the bytecode that will live at the contract’s address. So, while inside a constructor, your address (address(this)) will be the deployment address, but there will be no bytecode at that address! So if we check address(this).code.length before the constructor has finished, even from within a delegatecall, we will get 0.So now let’s update our initialize() function to only run if we are inside a constructor for that add this code to initialise() function -
`require(address(this).code.length == 0, 'not in constructor');`

Now the Proxy contract’s constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero.

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!

# [G-05] Sort Solidity operations using short-circuit mode
Short-circuiting is a solidity contract development model that uses `OR/AND` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

# [G-06] Events is missing indexed fields
Index event fields make the field more quickly accessible to off-chain.
Each event should use three indexed fields if there are three or more fields,this makes the code more gas optimized.