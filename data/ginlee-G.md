[G-01]Use constants instead of type(uintx).max
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L23
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L82-L83
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L23

type(uintx).max uses more gas in the distribution process and also for each transaction than constant usage.

uint256 constant internal PAUSE_ALL = 2**256 -1

use this instead of type(uint256).max