# [G-01]

## Refactor `sharesToUnderlyingView()` the function that may result in saving gas

### Proof Of Concept

```
function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
        if (totalShares == 0) {
            return amountShares;
        } else {
            return (_tokenBalance() * amountShares) / totalShares;
        }
    }
```



### Recommended Mitigation Steps

```
function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
    if (totalShares == 0) {
        return amountShares;
    }
    
    uint256 tokenBalance = _tokenBalance();
    
    // Check for edge case where amountShares is equal to totalShares
    if (amountShares == totalShares) {
        return tokenBalance;
    }

    uint256 underlyingAmount = tokenBalance * amountShares;

    // The division can be moved outside the else block to save gas when totalShares is not equal to zero
    return underlyingAmount / totalShares;
}
```

Declared a tokenBalance variable to store the `_tokenBalance()` value once, instead of calling the function twice.
Added a check for an edge case where `amountShares` is equal to `totalShares`, which avoids performing a multiplication and division that would return the same value.
Moved the division operation outside the else block to avoid performing unnecessary calculations when totalShares is equal to zero.

##### Reference 

[StrategyBase.sol#L172-178](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#LL172C4-L178C6)

# [G-02]

## Use Import Aliases To Reduce Gas Cost

### Proof Of Concept 

```
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
```

### Recommended Mitigation Steps

```
import { IERC20 as ERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { Initializable } from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

##### References
[StrategyBase.sol#L6-8](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#LL6C1-L8C73)
[StrategyManager.sol#L4-10](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#LL4C1-L10C63)
[EigenPodManager.sol#L4-9](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#LL4C1-L9C73)
[EigenPod.sol#L4-7](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#LL4C1-L7C72)
[DelayedWithdrawalRouter.sol#L4-6](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#LL4C1-L6C83)


