Wrong information on the page announcing the match:

On the webpage announcing the competition, the number of lines of the most important contract of the project is 414 lines, while in reality there are 890 lines of programming in the contract file.
===========================================================================
For the basis of” The Saltzer and Schroeder's design principles”, The design of the project should be as simple as possible and avoid complexity. In this project, the main contract (StrategyManager.sol)is written in a large and complicated file. , and this issue increases the possibility of hiding a hack possibility or error point.

Suggestion: The main contract should be divided into two or more smaller contracts so that the review of each one separately provides better quality in the audit.


=======================================================================

Using a customized error message consumes less gas than sending a message in the description field of the require. They can be corrected in the below code:

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L96


https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L104


https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L114


https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L119

=======================================================================


https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L115


A typecast for the address variable in the above command line is required and should be modified as follows:

        require(msg.sender == address( strategyWhitelister), "StrategyManager.onlyStrategyWhitelister: not the strategyWhitelister");

=======================================================================
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L846


 The owner may intentionally or unintentionally set this address to zero. Therefore, it is recommended to include a  require command in the function body to check that the address in the input argument is not zero.

require ( != 0, “it can't be zero)
=======================================================================

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L70


 Wrong input information
=======================================================================


https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L72


All three input argument needed to be checked for zero address 


=======================================================================


Note: This problem is related to one of the contracts outside the scope of the competition, but according to the logic of the  project and possible scenarios, it can cause problems:


In the  below function, anyone can delegate to himself again and register the delegation points for himself. A mechanism should be created in the  below function so that it is not possible for a person to delegate to himself.  It can easily done by comparing msg.sender with input argument.

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManager.sol#L84


