# [L-01] Code Implemented wrongly
In line 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L372

it is written // if the withdrawal amount is greater than the REQUIRED_BALANCE_GWEI (i.e. the amount restaked on EigenLayer, per ETH validator)

but in next line it is implemented as:  
if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI)

In the same way at line 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L386

but implemented as if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI)
.
### Impact:
All times when (withdrawalAmountGwei = REQUIRED_BALANCE_GWEI),amountToSend also becomes zero affecting the entire logic of the code. 

### Recommendation:
Implement the code correctly. 

# [L-02] Wrong Comments
In line
 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L194

but implemented in next line as (validatorCurrentBalanceGwei >= REQUIRED_BALANCE_GWEI).
The failure statement provided in require statement says  "EigenPod.verifyCorrectWithdrawalCredentials: ETH validator's balance must be greater than or equal to the restaked balance per validator" 
which inturn means the implementation of code is correct but the comment given is wrong.
### Recommendation:
Correct the comment given the specified line of code.

# [L-03] Difference in assumption and implementation in the code.
In line 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L265

it is given -  // If the validator *has* been slashed, then this function can proceed. If they have *not* been slashed, then the `verifyAndProcessWithdrawal` function should be called instead.

Then the line 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L270

should have implemented if-else instead of require,where 'if' would have condition "slashedStatus == 1" and 'else' would call the function "verifyAndProcessWithdrawal" and the function visibility of the function should be changed to 'public'.

# [NC-01] Formatting issue
The arrangement of comments of parameters does not align with their implementation order in functions. 
### Occurences:
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L47-L53

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L124-L128

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L141-L145

# [NC-02] No comments given in multiple areas where explaination was needed 

Some Occurences:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L96-L122

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L754

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L765

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L135

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L360

# [NC-03] Calculation done in comment gives wrong result in Solidity because of rounding.
In this line 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L45

it should be done as:(60 * 60 * 24 * 7)/ 12 = 50,400

# [NC-04] Comments not needed here
Comments are not needed at
 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L445

and

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L463

as queueWithdrawal already has a  onlyNotFrozen modifier in it.

# [NC-05] Misplacement of comments
The comment 
//check that the user has sufficient shares

should have been placed at 

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L689

instead of 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L687 
