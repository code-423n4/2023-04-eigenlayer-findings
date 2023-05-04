## GAS-1: <X> <= <Y> costs more gas than <X> < <Y> + 1

### Description

In Solididy, the opcode 'less or equal' doesn't exist. So the EVM will translate this by two distinct operation, first the inferior, and then the equal which cost more gas then a strict less.

### Affected file

* DelayedWithdrawalRouter.sol (Line: 133, 167)
* EigenPod.sol (Line: 195, 249, 354, 373, 387)
* Merkle.sol (Line: 50, 101)
* StrategyBase.sol (Line: 106, 139)
* StrategyManager.sol (Line: 690, 761, 840)

## GAS-2: Cache the mapping values rather than fetch it every time

### Affected file

* DelayedWithdrawalRouter.sol (Line:  111, 112, 116)
* EigenPod.sol (Line: 333, 355)

## GAS-3: Change ```public``` state variable visibility to ```private```

### Description

If it is preferred to change the visibility of the ```owner``` state variable to private, this will save significant gas.

### Affected file

* EigenPod.sol (Line: 59)
* EigenPodManager.sol (Line: 55)

## GAS-4: Duplicated require()/revert() checks should be refactored to a modifier or function

### Description

This saves deployment gas.

### Affected file

* DelayedWithdrawalRouter.sol (Line: 159)
* EigenPod.sol (Line: 115, 218, 373, 387, 417)
* EigenPodManager.sol (Line: 72, 114, 196)
* Merkle.sol (Line: 51, 102)
* StrategyBase.sol (Line: 41, 86, 92, 106, 128, 139, 173, 198)
* StrategyManager.sol (Line: 359, 507, 542, 548, 567, 631, 684, 750, 778, 781)

## GAS-5: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

### Affected file

* BeaconChainProofs.sol (Line: 130, 140, 150, 160, 178, 192, 221, 245)
* Merkle.sol (Line: 29, 80, 129)

## GAS-6: Not using the named return variables when a function returns, wastes deployment gas

### Description

It is not necessary to have both a named return and a return statement.

### Affected file

* StrategyBase.sol (Line: 85)
* StrategyManager.sol (Line: 659)

## GAS-7: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* DelayedWithdrawalRouter.sol (Line: 39)
* EigenPod.sol (Line: 99, 104, 109, 114, 120, 131)
* EigenPodManager.sol (Line: 66, 71)
* Pausable.sol (Line: 32, 37, 43, 49)
* PauserRegistry.sol (Line: 21)
* StrategyBase.sol (Line: 40)
* StrategyManager.sol (Line: 96, 104, 109, 114, 119)

## GAS-8: Use ```assembly``` to write address storage values

### Description



### Affected file

* DelayedWithdrawalRouter.sol (Line: 46, 169)
* EigenPod.sol (Line: 142, 143, 144, 145, 146, 154, 219, 455)
* EigenPodManager.sol (Line: 77, 78, 79, 80, 187)
* Pausable.sol (Line: 60, 62, 74, 82, 95)
* PauserRegistry.sol (Line: 44, 50)
* StrategyBase.sol (Line: 47, 57, 110, 142)
* StrategyManager.sol (Line: 133)
* StrategyManagerStorage.sol (Line: 73, 74, 75)

## GAS-9: Use assembly to check for address(0)

### Description

Saves 6 gas per instance if using assembly to check for address(0).

### Remediation

```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

### Affected file

* DelayedWithdrawalRouter.sol (Line: 45)
* EigenPod.sol (Line: 153)
* Pausable.sol (Line: 56, 56)
* PauserRegistry.sol (Line: 42, 48)
* StrategyManager.sol (Line: 343, 631, 684)

## GAS-10: Use constants instead of type(uintx).max

### Description

It uses more gas in the distribution process and also for each transaction than constant usage.

### Affected file

* Pausable.sol (Line: 82, 83)

## GAS-11: Use hardcoded address instead address(this)

### Description

Instead of using ```address(this)```, it is more gas-efficient to pre-calculate and use the hardcoded ```address```

### Affected file

* EigenPod.sol (Line: 456, 461)
* StrategyBase.sol (Line: 236, 242)
* StrategyManager.sol (Line: 150, 276)

## GAS-12: Use nested if and avoid multiple check combinations

### Description

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

### Affected file

* StrategyManager.sol (Line: 422, 562)

## GAS-13: Use selfbalance()

### Description

Use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas.

### Affected file

* EigenPod.sol (Line: 456)

## GAS-14: Using immutable on variables that are only set in the constructor and never after (2.1k gas per var)

### Description

Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas).

### Affected file

* DelayedWithdrawalRouter.sol (Line: 27)
* EigenPod.sol (Line: 44, 47, 50, 53, 56)
* EigenPodManager.sol (Line: 40, 43, 46, 49)
* StrategyBase.sol (Line: 31)
* StrategyManager.sol (Line: 42)
* StrategyManagerStorage.sol (Line: 31, 32, 33)

## GAS-15: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas.

### Affected file

* EigenPodManager.sol (Line: 175, 202)
* StrategyManager.sol (Line: 150, 268, 276, 879)