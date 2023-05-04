## [LC-1] USE SPECIFIC NAMED IMPORTS
Use specific named import to import specific contracts this way 

```
import {Pausable} from "../permissions/Pausable.sol";
```
instead of 

```
import "../permissions/Pausable.sol";
```

Files: All files.

## [LC-2] USE UPGRADABLE OPENZEPPELIN LIBRARIES FOR UPGRADABLE CONTRACTS
The StrategyManager.sol contract uses the Pausable.sol Openzeppelin library contract. Openzeppelin recommends using upgradable library contracts for your upgradable contracts.

File: https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L30

The StrategyManager.sol should inherit from PausableUpgradable instead of the Pausable contract.
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/security/PausableUpgradeable.sol

```
contract StrategyManager is
    Initializable,
    OwnableUpgradeable,
    ReentrancyGuardUpgradeable,
    Pausable,
    StrategyManagerStorage
{
```

## [LC-3] INVOKE ALL THE INITIALIZE FUNCTIONS IN THE PARENT CONTRACTS.
The Strategy manager contract inherits from the OwnableUpgradable and ReentrancyGuardUpgradable openzeppelin contracts but does not invoke their respective init functions as recommended by openzeppelin.

Invoke the init functions of the inherited upgradable contracts.

File: https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146-L155

```
function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer
    {
        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
        _initializePauser(_pauserRegistry, initialPausedStatus);
        _transferOwnership(initialOwner);
        _setStrategyWhitelister(initialStrategyWhitelister);
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }
```
#### Recommended Mitigation Steps
Call the `__Ownable_init()` and the init function of `ReentrancyGuardUpgradable` contract in the initialize function of `StrategyManager`

