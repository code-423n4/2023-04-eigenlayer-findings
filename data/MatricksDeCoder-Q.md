#### NC-1 Function Parameters Use of UnderScore

-Description => Contracts in scope make use _ underscore in front of function parameters as best practise. However certain instances as below are not consistent with other code

See examples below some functions with underscore params whereas other code parts don't use this

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L146 

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L582

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L839

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L72
All params use _ underscore in StrategyManagerStorage.sol 

StrategyBase.sol All function params don't use _ underscore

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L84
Some in above use _ underscore others dont

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L152
Underscores used like in constructor above contract but remaining functions dont use this

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L56
Some params use _ underscore others dont 

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/PauserRegistry.sol#L26
Above uses _ underscore params constructor and rest of functions in contract dont do that 

libraries/Merkle.sol, libraries/BeaconChainProofs.sol etc -> all function params dont make use of underscore 

-Impact => This can lead to inconsistencies in code or implied meaning of some parameters underscored 
-POC => Inconsistency 
-Tools Used => Manual
-Recommendation => It is recommended to make use of underscore _ before all function parameters or be consistent within the various functions. 


#### NC-2 Implicit use of uint

-Impact => This can lead to readability, maintenance or even make code prone to error if incorrect casting of values occurs

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L137

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L145

-POC => Recommended Best Practice
-Tools Used => Manual
-Recommendation => It is recommended to always be specific with the uint types e.g uint256. Fix all instances mentioned and other similar hay may not have been mentioned






