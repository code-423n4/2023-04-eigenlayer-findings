# [L-01]

## `deposit()` does not emit an event, So it is difficult to track changes in the value of `totalShares` offchain

### Proof Of Concept  

```
function deposit(IERC20 token, uint256 amount)
        external
        virtual
        override
        onlyWhenNotPaused(PAUSED_DEPOSITS)
        onlyStrategyManager
        returns (uint256 newShares)
    {
        require(token == underlyingToken, "StrategyBase.deposit: Can only deposit underlyingToken");

        /**
         * @notice calculation of newShares *mirrors* `underlyingToShares(amount)`, but is different since the balance of `underlyingToken`
         * has already been increased due to the `strategyManager` transferring tokens to this strategy prior to calling this function
         */
        if (totalShares == 0) {
            newShares = amount;
        } else {
            uint256 priorTokenBalance = _tokenBalance() - amount;
            if (priorTokenBalance == 0) {
                newShares = amount;
            } else {
                newShares = (amount * totalShares) / priorTokenBalance;
            }
        }

        // checks to ensure correctness / avoid edge case where share rate can be massively inflated as a 'griefing' sort of attack
        require(newShares != 0, "StrategyBase.deposit: newShares cannot be zero");
        uint256 updatedTotalShares = totalShares + newShares;
        require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES,
            "StrategyBase.deposit: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");

        // update total share amount
        totalShares = updatedTotalShares;
        return newShares;
    }
```

### Tools Used 

##### Slither

### Recommended Mitigation Steps

Emit events to notify about updated total shares.
```
L110:totalShares = updatedTotalShares;
//Should be updated with the emit  
emit TotalSharesUpdated(totalShares);

```
Emit The Event outside the `deposit()`
```
event TotalSharesUpdated(uint256 totalShares);
``` 

##### Reference 

[StrategyBase.sol#L110](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#LL110C9-L110C42)

### Same Mitigation Steps as [L-01] in `withdraw()` at #L142

##### Reference

[StrategyBase.sol#L142](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L142)

# [QA-01]

## `computePhase0BeaconBlockHeaderRoot()` is never used and should be removed

### Proof Of Concept 

```
 function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {
        bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
        
        for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {
            paddedHeaderFields[i] = blockHeaderFields[i];
        }

        return Merkle.merkleizeSha256(paddedHeaderFields);
    }
```
### Recommended Mitigation Steps

## `computePhase0BeaconBlockHeaderRoot()` Should be removed to reduce code & to not make a code's review difficult

##### Reference

[BeaconChainProofs.sol#L130-138](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#LL130C4-L138C6)

## `computePhase0BeaconStateRoot()` Is Never Used & Should Be Removed

##### Reference

[BeaconChainProofs.sol#L140-148](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#LL140C5-L148C6) 

## `computePhase0ValidatorRoot()` Is Never Used & Should Be Removed

##### Reference

[BeaconChainProofs.sol#L150-158](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#LL150C5-L159C1)


## `computePhase0Eth1DataRoot()` Is Never Used & Should Be Removed

##### Reference

[BeaconChainProofs.sol#L160-168](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#LL160C5-L168C6)







