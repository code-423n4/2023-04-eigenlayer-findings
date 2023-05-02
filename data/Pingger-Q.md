## L-01

# Title
Missing events arithmetic

# Vulnerable End Points

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L78-L112

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L121-L156

# Description

Detected missing events for critical arithmetic parameters.

```solidity
StrategyBase.deposit(IERC20,uint256) should emit an event for totalShares = updatedTotalShares
src/contracts/strategies/StrategyBase.sol#L110
```

```solidity
StrategyBase.withdraw(address,IERC20,uint256) should emit an event for totalShares = updatedTotalShares
src/contracts/strategies/StrategyBase.sol#L142
```

# Exploit Scenario 1
```solidity
    function withdraw(address depositor, IERC20 token, uint256 amountShares)
        external
        virtual
        override
        onlyWhenNotPaused(PAUSED_WITHDRAWALS)
        onlyStrategyManager
    {
        require(token == underlyingToken, "StrategyBase.withdraw: Can only withdraw the strategy token");
        // copy `totalShares` value to memory, prior to any decrease
        uint256 priorTotalShares = totalShares;
        require(
            amountShares <= priorTotalShares,
            "StrategyBase.withdraw: amountShares must be less than or equal to totalShares"
        );


        // Calculate the value that `totalShares` will decrease to as a result of the withdrawal
        uint256 updatedTotalShares = priorTotalShares - amountShares;
        // check to avoid edge case where share rate can be massively inflated as a 'griefing' sort of attack
        require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
            "StrategyBase.withdraw: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");
        // Actually decrease the `totalShares` value
        totalShares = updatedTotalShares;


        /**
         * @notice calculation of amountToSend *mirrors* `sharesToUnderlying(amountShares)`, but is different since the `totalShares` has already
         * been decremented. Specifically, notice how we use `priorTotalShares` here instead of `totalShares`.
         */
        uint256 amountToSend;
        if (priorTotalShares == amountShares) {
            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
        }


        underlyingToken.safeTransfer(depositor, amountToSend);
    }

function explanation() external pure virtual override returns (string memory) {
        return "Base Strategy implementation to inherit from for more complex implementations";
    }
```
withdraw() does not emit an event, so it is difficult to track changes in the value of withdrawal amount off-chain.

# Recommendation
Emit an event for critical parameter changes.

# Exploit Scenario 2
```solidity
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

deposit() does not emit an event, so it is difficult to track changes in the value of deposit amount off-chain.

# Recommendation
Emit an event for critical parameter changes.

## NC-01

# Title
Dead-code

# Vulnerable End Points

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L130-L138

# Description
Functions that are not used.
The dead_codes are not used in the contracts, and make the codes` review more difficult.

# Exploit Scenario
```solidity
    function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {
        bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
        
        for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {
            paddedHeaderFields[i] = blockHeaderFields[i];
        }


        return Merkle.merkleizeSha256(paddedHeaderFields);
    }
```

# Recommendation
Remove unused functions.

## NC-02

# Title
Dead-code

# Vulnerable End Points

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L140-L148

# Description
Functions that are not used.
The dead_codes are not used in the contracts, and make the codes` review more difficult.

# Exploit Scenario
```solidity
    function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {
        bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);
        
        for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {
            paddedBeaconStateFields[i] = beaconStateFields[i];
        }
        
        return Merkle.merkleizeSha256(paddedBeaconStateFields);
    }
```

# Recommendation
Remove unused functions.

## NC-03

# Title
Dead-code

# Vulnerable End Points

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L150-L158

# Description
Functions that are not used.
The dead_codes are not used in the contracts, and make the codes` review more difficult.

# Exploit Scenario
```solidity
    function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {  
        bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);
        
        for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {
            paddedValidatorFields[i] = validatorFields[i];
        }


        return Merkle.merkleizeSha256(paddedValidatorFields);
    }
```

# Recommendation
Remove unused functions.

## NC-04

# Title
Dead-code

# Vulnerable End Points

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L160-L168

# Description
Functions that are not used.
The dead_codes are not used in the contracts, and make the codes` review more difficult.

# Exploit Scenario
```solidity
    function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {  
        bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);
        
        for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {
            paddedEth1DataFields[i] = eth1DataFields[i];
        }


        return Merkle.merkleizeSha256(paddedEth1DataFields);
    }
```

# Recommendation
Remove unused functions.