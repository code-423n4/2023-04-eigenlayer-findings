# VULN 1

## [GAS] Use != 0 instead of > 0 for unsigned integer comparison

---

### Proof of concept

Found in line 39 at EigenLayerContest/certora/harnesses/StructuredLinkedListHarness.sol:

        require(listStorage.remove(_node) > 0, "remove failed");

Found in line 75 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

                if (sharesAmount > 0) {

Found in line 73 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        require(_NUMBER_OF_QUORUMS <= 2 && _NUMBER_OF_QUORUMS > 0, "RegistryBase: NUMBER_OF_QUORUMS must be less than or equal to 2 and greater than 0");

Found in line 608 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            _operatorStake.firstQuorumStake > 0 || _operatorStake.secondQuorumStake > 0,

Found in line 643 at EigenLayerContest/src/test/EigenPod.t.sol:

        assertTrue(numStrats > 0, "_testDelegateToOperator: delegating from address with no deposits");

Found in line 109 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        cheats.assume(amountToDeposit > 0);

Found in line 251 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        cheats.assume(numStratsToAdd > 0 && numStratsToAdd <= 20);

Found in line 343 at EigenLayerContest/src/test/Delegation.t.sol:

        cheats.assume(numStratsToAdd > 0 && numStratsToAdd <= 20);

Found in line 66 at EigenLayerContest/src/test/mocks/Reenterer.sol:

        if (dataToReturnLength > 0) {

Found in line 95 at EigenLayerContest/src/test/mocks/Reenterer.sol:

        if (dataToReturnLength > 0) {

Found in line 63 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(amountToDeposit > 0 && amountToDeposit < MIN_NONZERO_TOTAL_SHARES);

Found in line 101 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(priorTotalShares >= MIN_NONZERO_TOTAL_SHARES && amountToDeposit > 0);

Found in line 209 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(fuzzedInput > 0 && fuzzedInput < MIN_NONZERO_TOTAL_SHARES);

---

### Mitigation

Use != 0 instead of > 0 for unsigned integer comparison.

# VULN 2

## [GAS] Use shift Right/Left instead of division/multiplication if possible

---

### Proof of concept

Found in line 368 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        uint32 diff = (toTaskNumber - fromTaskNumber) / 2;

Found in line 78 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(stakeSigned >= 666667 * uint256(totalStake) / 1000000, "Need more than 2/3 of stake to sign");

Found in line 206 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        cheats.roll(block.number + delayedWithdrawalRouter.withdrawalDelayBlocks() / 2);

Found in line 220 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        cheats.roll(block.number + delayedWithdrawalRouter.withdrawalDelayBlocks() / 2);

---

### Mitigation

Use shift Right/Left instead of division/multiplication if possible.

# VULN 3

## [GAS] ++i/i++ should be unchecked{++i}/unchecked{i++} and ++i costs less gas than i++

---

### Proof of concept

Found in line 126 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 136 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 91 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 101 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 51 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        for (uint256 i = 0; i < 10; i++) {

Found in line 88 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        taskNumber++;

Found in line 172 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {

Found in line 223 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {

Found in line 31 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i; i < stakers.length; i++) {

Found in line 429 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 453 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 477 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 494 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 46 at EigenLayerContest/src/test/Slasher.t.sol:

        for (uint256 i = 0; i < accounts.length; i++) {

Found in line 29 at EigenLayerContest/src/test/utils/Owners.sol:

        for (uint256 i = 0; i < getNumOperators(); i++) {

Found in line 37 at EigenLayerContest/src/test/utils/Owners.sol:

        for (uint256 i = 0; i < getNumOperators(); i++) {

Found in line 70 at EigenLayerContest/src/test/utils/Operators.sol:

        for (uint i = 0; i < b.length; i++) {

Found in line 77 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 7; i++) {

Found in line 85 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 4; i++) {

Found in line 93 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 18; i++) {

Found in line 101 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 3; i++) {

Found in line 109 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 9; i++) {

Found in line 117 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 46; i++) {

Found in line 126 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 4; i++) {

Found in line 138 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 8; i++) {

Found in line 147 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 44; i++) {

Found in line 156 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 46; i++) {

Found in line 18 at EigenLayerContest/script/DepositAndDelegate.s.sol:

        for (uint256 i = 0; i < numStaker; i++) {

Found in line 10 at EigenLayerContest/script/utils/Allocator.sol:

        for (uint i = 0; i < recipients.length; i++) {

---

### Mitigation

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for and while-loops. Moreover ++i costs less gas than i++, especially when its used in for-loops (--i/i-- too).

# VULN 4

## [GAS] Don’t initialize variables with default value

---

### Proof of concept

Found in line 35 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        for (uint256 i = 0; i < strategiesLength;) {

Found in line 65 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        for (uint256 i = 0; i < length; ++i) {

Found in line 75 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        uint256 res = 0;

Found in line 76 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        for (uint256 i = 0; i < length; ++i) {

Found in line 87 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        uint256 res = 0;

Found in line 89 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        for (uint256 i = 0; i < length; ++i) {

Found in line 126 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 136 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 244 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        for (uint256 i = 0; i < operatorsLength;) {

Found in line 67 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

            for (uint256 i = 0; i < stratsLength;) {

Found in line 116 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

        for (uint256 i = 0; i < numStrats;) {

Found in line 151 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

        for (uint256 i = 0; i < numStrats;) {

Found in line 189 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

        for (uint256 i = 0; i < numStratsToAdd;) {

Found in line 191 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

            for (uint256 j = 0; j < (numStratsExisting + i);) {

Found in line 91 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 101 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 171 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint256 i = 0; i < operatorsLength;) {

Found in line 26 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public taskNumber = 0;

Found in line 27 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public latestServeUntilBlock = 0;

Found in line 51 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        for (uint256 i = 0; i < 10; i++) {

Found in line 68 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        uint128 stakeSigned = 0;

Found in line 69 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        for(uint256 i = 0; i < signatures.length; i += 65) {

Found in line 111 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        for (uint i = 0; i < signatures.length; i += 65) {

Found in line 185 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

            for (uint256 i = 0; i < stratsLength;) {

Found in line 321 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        for (uint256 i = 0; i < stratsLength;) {

Found in line 128 at EigenLayerContest/src/contracts/core/Slasher.sol:

        for (uint256 i = 0; i < frozenAddresses.length;) {

Found in line 86 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {

Found in line 131 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {

Found in line 172 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {

Found in line 223 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {

Found in line 267 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        uint256 beta = 0;

Found in line 268 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        uint256 y = 0;

Found in line 22 at EigenLayerContest/src/test/EigenPod.t.sol:

    uint40 validatorIndex0 = 0;

Found in line 177 at EigenLayerContest/src/test/EigenPod.t.sol:

        uint256 initPausedStatus = 0;

Found in line 645 at EigenLayerContest/src/test/EigenPod.t.sol:

        for (uint256 i = 0; i < numStrats; ++i) {

Found in line 662 at EigenLayerContest/src/test/EigenPod.t.sol:

        for (uint256 i = 0; i < numStrats; ++i) {

Found in line 218 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < numStrats; ++i) {

Found in line 235 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < numStrats; ++i) {

Found in line 255 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint8 i = 0; i < numStratsToAdd; ++i) {

Found in line 267 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint8 i = 0; i < numStratsToAdd; ++i) {

Found in line 429 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 453 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 477 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 494 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 351 at EigenLayerContest/src/test/Delegation.t.sol:

        for (uint16 i = 0; i < numStratsToAdd; ++i) {

Found in line 209 at EigenLayerContest/src/test/EigenLayerDeployer.t.sol:

        uint256 initPausedStatus = 0;

Found in line 46 at EigenLayerContest/src/test/Slasher.t.sol:

        for (uint256 i = 0; i < accounts.length; i++) {

Found in line 18 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

    uint256 public initPausedStatus = 0;

Found in line 37 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        for (uint256 i = 0; i < numberPotentialOracleSigners; ++i) {

Found in line 107 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        for (uint256 i = 0; i < amountSignersToAdd; ++i) {

Found in line 117 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        for (uint256 i = 0; i < amountSignersToAdd; ++i) {

Found in line 178 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        for (uint256 i = 0; i < amountSignersToRemove; ++i) {

Found in line 188 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        for (uint256 i = 0; i < amountSignersToRemove; ++i) {

Found in line 250 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        for (uint256 i = 0; i < beaconChainOracle.threshold(); ++i) {

Found in line 307 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        for (uint256 i = 0; i < numberOfOperators; ++i) {

Found in line 323 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        for (uint256 i = 0; i < numberOfOperators; ++i) {

Found in line 420 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock = 0;

Found in line 437 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock = 0;

Found in line 450 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock = 0;

Found in line 580 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock = 0;

Found in line 602 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock = 0;

Found in line 625 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock = 0;

Found in line 171 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 msgValueToUse = 0;

Found in line 185 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 beaconChainETHStrategyIndex = 0;

Found in line 201 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 beaconChainETHStrategyIndex = 0;

Found in line 214 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 beaconChainETHStrategyIndex = 0;

Found in line 221 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 msgValueToUse = 0;

Found in line 304 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 msgValueToUse = 0;

Found in line 476 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            uint256 msgValueToUse = 0;

Found in line 854 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 962 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 988 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1020 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1091 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1095 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 msgValueToUse = 0;

Found in line 1141 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1168 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1198 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1227 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1252 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1278 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 middlewareTimesIndex = 0;

Found in line 1565 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 msgValueToUse = 0;

Found in line 1701 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 msgValueToUse = 0;

Found in line 1766 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        for (uint256 i = 0; i < MAX_STAKER_STRATEGY_LIST_LENGTH; ++i) {

Found in line 2056 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        for (uint256 i = 0; i < numberOfStrategiesToAdd; ++i) {

Found in line 2066 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        for (uint256 i = 0; i < numberOfStrategiesToAdd; ++i) {

Found in line 2097 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        for (uint256 i = 0; i < numberOfStrategiesToRemove; ++i) {

Found in line 2105 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        for (uint256 i = 0; i < numberOfStrategiesToAdd; ++i) {

Found in line 58 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint256 initPausedStatus = 0;

Found in line 79 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint256 initPausedStatus = 0;

Found in line 108 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint224 delayedWithdrawalAmount = 0;

Found in line 138 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        for (uint256 i = 0; i < delayedWithdrawalsToCreate; ++i) {

Found in line 174 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint256 totalDelayedWithdrawalAmount = 0;

Found in line 175 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        for (uint256 i = 0; i < numberOfDelayedWithdrawalsThatShouldBeCompleted; ++i) {

Found in line 196 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        for (uint256 i = 0; i < delayedWithdrawalsToCreate; ++i) {

Found in line 210 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        for (uint256 i = 0; i < delayedWithdrawalsToCreate; ++i) {

Found in line 247 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint256 totalDelayedWithdrawalAmount = 0;

Found in line 248 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        for (uint256 i = 0; i < numberOfDelayedWithdrawalsThatShouldBeCompleted; ++i) {

Found in line 256 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 delayedWithdrawalsToCreate = 0;

Found in line 257 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 maxNumberOfDelayedWithdrawalsToClaim = 0;

Found in line 263 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 delayedWithdrawalsToCreate = 0;

Found in line 271 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 maxNumberOfDelayedWithdrawalsToClaim = 0;

Found in line 296 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint256 msgValueToUse = 0;

Found in line 74 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        uint256 amountToDeposit = 0;

Found in line 29 at EigenLayerContest/src/test/utils/Owners.sol:

        for (uint256 i = 0; i < getNumOperators(); i++) {

Found in line 37 at EigenLayerContest/src/test/utils/Owners.sol:

        for (uint256 i = 0; i < getNumOperators(); i++) {

Found in line 69 at EigenLayerContest/src/test/utils/Operators.sol:

        uint result = 0;

Found in line 70 at EigenLayerContest/src/test/utils/Operators.sol:

        for (uint i = 0; i < b.length; i++) {

Found in line 77 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 7; i++) {

Found in line 85 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 4; i++) {

Found in line 93 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 18; i++) {

Found in line 101 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 3; i++) {

Found in line 109 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 9; i++) {

Found in line 117 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 46; i++) {

Found in line 126 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 4; i++) {

Found in line 138 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 8; i++) {

Found in line 147 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 44; i++) {

Found in line 156 at EigenLayerContest/src/test/utils/ProofParsing.sol:

        for (uint i = 0; i < 46; i++) {

Found in line 18 at EigenLayerContest/script/DepositAndDelegate.s.sol:

        for (uint256 i = 0; i < numStaker; i++) {

Found in line 23 at EigenLayerContest/script/Allocate.s.sol:

        for (uint i = 0; i < numStaker ; ++i) {

Found in line 34 at EigenLayerContest/script/Allocate.s.sol:

        for (uint i = 0; i < numDis ; ++i) {

Found in line 230 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < strategyTokensAndNames.length; ++i) {

Found in line 248 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < strategyTokensAndNames.length; ++i) {

Found in line 292 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < strategyTokensAndNames.length; ++i) {

Found in line 377 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < deployedStrategyArray.length; ++i) {

Found in line 408 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < deployedStrategyArray.length; ++i) {

Found in line 96 at EigenLayerContest/script/utils/ExistingDeploymentParser.sol:

        for (uint256 i = 0; i < strategyList.length; ++i) {

Found in line 10 at EigenLayerContest/script/utils/Allocator.sol:

        for (uint i = 0; i < recipients.length; i++) {

---

### Mitigation

In such cases, initializing the variables with default values would be unnecessary and can be considered a waste of gas. Additionally, initializing variables with default values can sometimes lead to unnecessary storage operations, which can increase gas costs. For example, if you have a large array of variables, initializing them all with default values can result in a lot of unnecessary storage writes, which can increase the gas costs of your contract.

# VULN 5

## [GAS] Use Custom Errors

---

### Proof of concept

Found in line 32 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        require(tokens.length == strategies.length, "StrategyManager.slashShares: input length mismatch");

Found in line 31 at EigenLayerContest/certora/harnesses/StructuredLinkedListHarness.sol:

            require(listStorage.insertAfter(_node, _new), "_insert failed");

Found in line 33 at EigenLayerContest/certora/harnesses/StructuredLinkedListHarness.sol:

            require(listStorage.insertBefore(_node, _new), "_insert failed");

Found in line 39 at EigenLayerContest/certora/harnesses/StructuredLinkedListHarness.sol:

        require(listStorage.remove(_node) > 0, "remove failed");

Found in line 67 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        require(operatorWhitelister == msg.sender, "BLSRegistry.onlyOperatorWhitelister: not operatorWhitelister");

Found in line 161 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

            require(whitelisted[operator], "BLSRegistry._registerOperator: not whitelisted");

Found in line 170 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        require(pubkeyHash != ZERO_PK_HASH, "BLSRegistry._registerOperator: Cannot register with 0x0 public key");

Found in line 242 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        require(operatorsLength == prevElements.length, "BLSRegistry.updateStakes: prevElement is not the same length as operators");

Found in line 348 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        require(_operatorWhitelister != address(0), "BLSRegistry.initialize: cannot set operatorWhitelister to zero address");

Found in line 359 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        require(blockNumber >= _apkUpdates[index].blockNumber, "BLSRegistry.getCorrectApkHash: index too recent");

Found in line 364 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

            require(blockNumber < _apkUpdates[index + 1].blockNumber, "BLSRegistry.getCorrectApkHash: Not latest valid apk update");

Found in line 280 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            require(uint256(pubkeyHash) > uint256(pubkeyHashes[i - 1]), "BLSSignatureChecker.checkSignatures: Pubkey hashes must be in ascending order");

Found in line 308 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            require(success, "BLSSignatureChecker.checkSignatures: non signer addition failed");

Found in line 363 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            require(success, "BLSSignatureChecker.checkSignatures: aggregate non signer addition failed");

Found in line 412 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: aggregate signer public key random shift failed");

Found in line 422 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: aggregate signer public key and signature addition failed");

Found in line 434 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: generator random shift failed");

Found in line 441 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: generator random shift and G1 hash addition failed");

Found in line 466 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: pairing precompile call failed");

Found in line 468 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        require(input[0] == 1, "BLSSignatureChecker.checkSignatures: Pairing unsuccessful");

Found in line 496 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        require(opStake.updateBlockNumber <= referenceBlockNumber, "Provided stake index is too early");

Found in line 26 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

        require(msg.sender == serviceManager.owner(), "onlyServiceManagerOwner");

Found in line 52 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

        require(totalQuorumBips == MAX_BIPS, "VoteWeigherBase._initialize: totalQuorumBips != MAX_BIPS");

Found in line 114 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

        require(indicesToRemove.length == numStrats, "VoteWeigherBase.removeStrategiesConsideredAndWeights: input length mismatch");

Found in line 49 at EigenLayerContest/src/contracts/middleware/BLSPublicKeyCompendium.sol:

        require(shouldBeZero.X == 0 && shouldBeZero.Y == 0, "BLSPublicKeyCompendium.registerBLSPublicKey: incorrect schnorr singature");

Found in line 63 at EigenLayerContest/src/contracts/middleware/BLSPublicKeyCompendium.sol:

        require(pubkeyHash != ZERO_PK_HASH, "BLSPublicKeyCompendium.registerBLSPublicKey: operator attempting to register the zero public key");

Found in line 73 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        require(_NUMBER_OF_QUORUMS <= 2 && _NUMBER_OF_QUORUMS > 0, "RegistryBase: NUMBER_OF_QUORUMS must be less than or equal to 2 and greater than 0");

Found in line 666 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        require(operator == operatorList[index], "RegistryBase._deregistrationCheck: Incorrect index supplied");

Found in line 67 at EigenLayerContest/src/contracts/middleware/VoteWeigherBaseStorage.sol:

        require(_NUMBER_OF_QUORUMS != 0, "VoteWeigherBaseStorage.constructor: _NUMBER_OF_QUORUMS == 0");

Found in line 100 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        require(msg.sender == address(serviceManager), "onlyServiceManager");

Found in line 106 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        require(msg.sender == address(registry), "onlyRegistry");

Found in line 112 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        require(msg.sender == serviceManager.owner(), "onlyServiceManagerOwner");

Found in line 182 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        require(toTaskNumber <= _taskNumber(), "PaymentManager.commitPayment: Cannot claim future payments");

Found in line 211 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        require(fromTaskNumber < toTaskNumber, "invalid payment range");

Found in line 39 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        require(operatorWhitelister == msg.sender, "BLSRegistry.onlyOperatorWhitelister: not operatorWhitelister");

Found in line 121 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

            require(whitelisted[operator], "BLSRegistry._registerOperator: not whitelisted");

Found in line 169 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        require(operatorsLength == prevElements.length, "BLSRegistry.updateStakes: prevElement is not the same length as operators");

Found in line 197 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        require(_operatorWhitelister != address(0), "BLSRegistry.initialize: cannot set operatorWhitelister to zero address");

Found in line 33 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(msg.sender == address(registry), "Only registry can call this function");

Found in line 65 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(certifiedMessageMetadatas[message].validAfterBlock == 0, "Message already certified");

Found in line 67 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(msg.sender == tx.origin, "EOA must call this function");

Found in line 72 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

            require(registry.isActiveOperator(signer), "Signer is not an active operator");

Found in line 78 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(stakeSigned >= 666667 * uint256(totalStake) / 1000000, "Need more than 2/3 of stake to sign");

Found in line 105 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(certifiedMessageMetadata.validAfterBlock > block.number, "Dispute period has passed");

Found in line 107 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(certifiedMessageMetadata.signaturesHash == keccak256(signatures), "Signatures do not match");

Found in line 109 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        require(decaHash(message) >> (256 - numZeroes) == 0, "Message does not hash to enough zeroes");

Found in line 67 at EigenLayerContest/src/contracts/operators/MerkleDelegationTerms.sol:

        require(height <= MAX_HEIGHT, "MerkleDelegationTerms.postMerkleRoot: height input too large");

Found in line 95 at EigenLayerContest/src/contracts/operators/MerkleDelegationTerms.sol:

        require(proof.length == 32 * merkleRoots[rootIndex].height, "MerkleDelegationTerms.proveEarningsAndWithdraw: incorrect proof length");

Found in line 35 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        require(msg.sender == address(strategyManager), "onlyStrategyManager");

Found in line 97 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        require(expiry >= block.timestamp, "DelegationManager.delegateToBySignature: delegation signature expired");

Found in line 137 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        require(!isOperator(staker), "DelegationManager.undelegate: operators cannot undelegate from themselves");

Found in line 309 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        require(isNotDelegated(staker), "DelegationManager._delegate: staker has existing delegation");

Found in line 311 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        require(!slasher.isFrozen(operator), "DelegationManager._delegate: cannot delegate to a frozen operator");

Found in line 105 at EigenLayerContest/src/contracts/core/Slasher.sol:

        require(delegation.isOperator(msg.sender), "Slasher.optIntoSlashing: msg.sender is not a registered operator");

Found in line 171 at EigenLayerContest/src/contracts/core/Slasher.sol:

        require(updateBlock <= block.number, "Slasher.recordStakeUpdate: cannot provide update for future block");

Found in line 385 at EigenLayerContest/src/contracts/core/Slasher.sol:

        require(serveUntilBlock != MAX_CAN_SLASH_UNTIL, "Slasher._revokeSlashingAbility: serveUntilBlock time must be limited");

Found in line 53 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        require(isOracleSigner[msg.sender], "BeaconChainOracle.onlyOracleSigner: Not an oracle signer");

Found in line 105 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        require(!hasVoted[blockNumber][msg.sender], "BeaconChainOracle.voteForBeaconChainStateRoot: Signer has already voted");

Found in line 106 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        require(beaconStateRootAtBlockNumber[blockNumber] == bytes32(0), "BeaconChainOracle.voteForBeaconChainStateRoot: State root already confirmed");

Found in line 124 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        require(_threshold >= MINIMUM_THRESHOLD, "BeaconChainOracle._setThreshold: cannot set threshold below MINIMUM_THRESHOLD");

Found in line 126 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        require(success, "ec-add-failed");

Found in line 152 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        require(success, "ec-mul-failed");

Found in line 202 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        require(success, "pairing-opcode-failed");

Found in line 321 at EigenLayerContest/src/contracts/libraries/BN254.sol:

        require(success, "BN254.expMod: call failure");

Found in line 214 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_length + 31 >= _length, "slice_overflow");

Found in line 215 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + _length, "slice_outOfBounds");

Found in line 273 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 20, "toAddress_outOfBounds");

Found in line 284 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 1, "toUint8_outOfBounds");

Found in line 295 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 2, "toUint16_outOfBounds");

Found in line 306 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 4, "toUint32_outOfBounds");

Found in line 317 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 8, "toUint64_outOfBounds");

Found in line 328 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 12, "toUint96_outOfBounds");

Found in line 339 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 16, "toUint128_outOfBounds");

Found in line 350 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 32, "toUint256_outOfBounds");

Found in line 361 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 32, "toBytes32_outOfBounds");

Found in line 30 at EigenLayerContest/src/contracts/strategies/StrategyWrapper.sol:

        require(msg.sender == address(strategyManager), "StrategyWrapper.onlyStrategyManager");

Found in line 52 at EigenLayerContest/src/contracts/strategies/StrategyWrapper.sol:

        require(token == underlyingToken, "StrategyWrapper.deposit: Can only deposit underlyingToken");

Found in line 69 at EigenLayerContest/src/contracts/strategies/StrategyWrapper.sol:

        require(token == underlyingToken, "StrategyWrapper.withdraw: Can only withdraw the strategy token");

Found in line 214 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(pod.hasRestaked() == false, "Pod should not be restaked");

Found in line 220 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(_getLatestDelayedWithdrawalAmount(podOwner) == stakeAmount, "Payment amount should be stake amount");

Found in line 221 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(pod.mostRecentWithdrawalBlockNumber() == uint64(block.number), "Most recent withdrawal block number not updated");

Found in line 249 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(address(pod).balance == 0, "Pod balance should be 0");

Found in line 306 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(address(podOwner).balance - podOwnerBalanceBefore == leftOverBalanceWEI, "Pod owner balance hasn't been updated correctly");

Found in line 335 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(newPod.provenPartialWithdrawal(validatorIndex, slot), "provenPartialWithdrawal should be true");

Found in line 343 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(address(podOwner).balance - podOwnerBalanceBefore == withdrawalAmountGwei, "Pod owner balance hasn't been updated correctly");

Found in line 393 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(beaconChainETHShares == 0, "strategyManager shares not updated correctly");

Found in line 716 at EigenLayerContest/src/test/EigenPod.t.sol:

        require(beaconChainETHShares == REQUIRED_BALANCE_WEI, "strategyManager shares not updated correctly");

Found in line 302 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        require(amountToDeposit >= shareAmounts[0], "_createQueuedWithdrawal: sanity check failed");

Found in line 196 at EigenLayerContest/src/test/Delegation.t.sol:

        require(delegation.delegatedTo(staker) == address(0), "undelegation unsuccessful");

Found in line 280 at EigenLayerContest/src/test/Slasher.t.sol:

        require(frozen,"operator should be frozen");

Found in line 308 at EigenLayerContest/src/test/Slasher.t.sol:

        require(!frozen,"operator should be unfrozen");

Found in line 328 at EigenLayerContest/src/test/Slasher.t.sol:

        require(slasher.canSlash(operator,middleware),"middlewre should be able to slash");

Found in line 336 at EigenLayerContest/src/test/Slasher.t.sol:

        require(!slasher.canSlash(operator,middleware),"middlewre should no longer be able to slash");

Found in line 255 at EigenLayerContest/src/test/Whitelister.t.sol:

            require(dummyToken.balanceOf(staker) == balanceBeforeWithdrawal + AMOUNT, "balance not incremented");

Found in line 323 at EigenLayerContest/src/test/Whitelister.t.sol:

        require(dummyToken.balanceOf(receiver) == AMOUNT, "receiver hasn't received tokens");

Found in line 141 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            require(slasher.getMiddlewareTimesIndexBlock(staker, 0) == 1, "middleware updateBlock update incorrect");

Found in line 142 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            require(slasher.getMiddlewareTimesIndexServeUntilBlock(staker, 0) == 5, "middleware serveUntil update incorrect");

Found in line 150 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            require(slasher.getMiddlewareTimesIndexBlock(staker, 1) == 1, "middleware updateBlock update incorrect");

Found in line 151 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            require(slasher.getMiddlewareTimesIndexServeUntilBlock(staker, 1) == 6, "middleware serveUntil update incorrect");

Found in line 153 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            require(slasher.getMiddlewareTimesIndexBlock(staker, 0) == 1, "middleware updateBlock update incorrect");

Found in line 154 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            require(slasher.getMiddlewareTimesIndexServeUntilBlock(staker, 0) == 5, "middleware serveUntil update incorrect");

Found in line 183 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(slasher.getMiddlewareTimesIndexBlock(staker, 2) == 1, "middleware updateBlock update incorrect");

Found in line 184 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(slasher.getMiddlewareTimesIndexServeUntilBlock(staker, 2) == 7, "middleware serveUntil update incorrect");

Found in line 191 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(slasher.getMiddlewareTimesIndexBlock(staker, 3) == 3, "middleware updateBlock update incorrect");

Found in line 192 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(slasher.getMiddlewareTimesIndexServeUntilBlock(staker, 3) == 10, "middleware serveUntil update incorrect");

Found in line 202 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(slasher.getMiddlewareTimesIndexBlock(staker, 4) == 3, "middleware updateBlock update incorrect");

Found in line 203 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(slasher.getMiddlewareTimesIndexServeUntilBlock(staker, 4) == 10, "middleware serveUntil update incorrect");

Found in line 345 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(amountToDeposit >= shareAmounts[0], "_createQueuedWithdrawal: sanity check failed");

Found in line 407 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(shares.length == 0, "Attacker deposit should fail due to minimum balances");

Found in line 410 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(wethStrat.sharesToUnderlyingView(shares[0]) >= (1900000000000000000), "user has lost more than 0.1 eth from frontrunning");

Found in line 542 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        require(success, "depositing stETH failed");

Found in line 20 at EigenLayerContest/src/test/mocks/LiquidStakingToken.sol:

        require(balanceOf(msg.sender) >= _amount, "insufficient balance.");

Found in line 30 at EigenLayerContest/src/test/mocks/MiddlewareRegistryMock.sol:

        require(slasher.canSlash(operator, address(serviceManager)), "Not opted into slashing");

Found in line 335 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");

Found in line 358 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(from != address(0), "ERC20: transfer from the zero address");

Found in line 359 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(to != address(0), "ERC20: transfer to the zero address");

Found in line 364 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");

Found in line 387 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(account != address(0), "ERC20: mint to the zero address");

Found in line 413 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(account != address(0), "ERC20: burn from the zero address");

Found in line 418 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(accountBalance >= amount, "ERC20: burn amount exceeds balance");

Found in line 444 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(owner != address(0), "ERC20: approve from the zero address");

Found in line 445 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(spender != address(0), "ERC20: approve to the zero address");

Found in line 462 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

            require(currentAllowance >= amount, "ERC20: insufficient allowance");

Found in line 596 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(from != address(0), "ERC20: transfer from the zero address");

Found in line 597 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(to != address(0), "ERC20: transfer to the zero address");

Found in line 602 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");

Found in line 136 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(from != address(0), "ERC20: transfer from the zero address");

Found in line 137 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(to != address(0), "ERC20: transfer to the zero address");

Found in line 142 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");

Found in line 165 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(account != address(0), "ERC20: mint to the zero address");

Found in line 188 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(account != address(0), "ERC20: burn from the zero address");

Found in line 193 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(accountBalance >= amount, "ERC20: burn amount exceeds balance");

Found in line 219 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(owner != address(0), "ERC20: approve from the zero address");

Found in line 220 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        require(spender != address(0), "ERC20: approve to the zero address");

Found in line 237 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

            require(currentAllowance >= amount, "ERC20: insufficient allowance");

Found in line 39 at EigenLayerContest/src/test/mocks/MiddlewareVoteWeigherMock.sol:

        require(slasher.canSlash(operator, address(serviceManager)), "Not opted into slashing");

Found in line 78 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

        require(address(getPod(podOwner)) == msg.sender, "EigenPodManager.onlyEigenPod: not a pod");

Found in line 83 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

        require(msg.sender == address(strategyManager), "EigenPodManager.onlyStrategyManager: not strategyManager");

Found in line 106 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

        require(!hasPod(msg.sender), "EigenPodManager.createPod: Sender already has a pod");

Found in line 179 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

        require(msg.sender == Ownable(address(strategyManager)).owner(), "EigenPods.withdrawPenalties: only strategyManager owner");

Found in line 48 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == previousPausedStatus, "previousPausedStatus not set correctly");

Found in line 54 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == newPausedStatus, "newPausedStatus not set correctly");

Found in line 71 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == previousPausedStatus, "previousPausedStatus not set correctly");

Found in line 77 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == type(uint256).max, "newPausedStatus not set correctly");

Found in line 97 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == previousPausedStatus, "previousPausedStatus not set correctly");

Found in line 108 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused(indexToPause), "index is not paused");

Found in line 119 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == previousPausedStatus, "previousPausedStatus not set correctly");

Found in line 125 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == newPausedStatus, "newPausedStatus not set correctly");

Found in line 146 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

        require(pausable.paused() == previousPausedStatus, "previousPausedStatus not set correctly");

Found in line 93 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(beaconChainOracle.isOracleSigner(signerToAdd), "signer not added");

Found in line 95 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

            require(totalSignersAfter == totalSignersBefore, "totalSigners incremented incorrectly");

Found in line 97 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

            require(totalSignersAfter == totalSignersBefore + 1, "totalSigners did not increment correctly");

Found in line 118 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

            require(beaconChainOracle.isOracleSigner(signerArray[i]), "signer not added");

Found in line 120 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(totalSignersAfter == totalSignersBefore + amountSignersToAdd, "totalSigners did not increment correctly");

Found in line 133 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(beaconChainOracle.isOracleSigner(oracleSigner), "signer improperly removed");

Found in line 147 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(!beaconChainOracle.isOracleSigner(oracleSigner), "signer improperly added");

Found in line 161 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(!beaconChainOracle.isOracleSigner(signerToRemove), "signer not removed");

Found in line 163 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

            require(totalSignersAfter == totalSignersBefore - 1, "totalSigners did not decrement correctly");

Found in line 165 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

            require(totalSignersAfter == totalSignersBefore, "totalSigners decremented incorrectly");

Found in line 189 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

            require(!beaconChainOracle.isOracleSigner(signerArray[i]), "signer not removed");

Found in line 192 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(totalSignersAfter + amountThatShouldHaveBeenRemoved == totalSignersBefore, "totalSigners did not decrement correctly");

Found in line 204 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(!beaconChainOracle.isOracleSigner(oracleSigner), "signer improperly added");

Found in line 218 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(!beaconChainOracle.isOracleSigner(oracleSigner), "signer improperly added");

Found in line 230 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(votesAfter == votesBefore + 1, "votesAfter != votesBefore + 1");

Found in line 231 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(beaconChainOracle.hasVoted(_blockNumber, oracleSigner), "vote not recorded as being cast");

Found in line 235 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

            require(beaconChainOracle.beaconStateRootAtBlockNumber(_blockNumber) == bytes32(0), "state root improperly confirmed");

Found in line 248 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

        require(votesBefore == 0, "something is wrong, state root should have zero votes before voting");

Found in line 192 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(operatorWhitelistedContractsLinkedListSize == 0, "operatorWhitelistedContractsLinkedListSize != 0");

Found in line 195 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(middlewareTimesLength >= 2, "middlewareTimesLength < 2");

Found in line 202 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(withdrawalStartBlock < olderMiddlewareTimes.stalestUpdateBlock, "withdrawalStartBlock >= olderMiddlewareTimes.stalestUpdateBlock");

Found in line 227 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.canSlash(operator, contractAddress), "contract was not properly granted slashing permission");

Found in line 246 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(!delegationMock.isOperator(notOperator), "caller is an operator -- this is assumed false");

Found in line 264 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.isFrozen(toBeFrozen), "operator not properly frozen");

Found in line 324 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(!slasher.isFrozen(operatorAddresses[i]), "operator frozen improperly (not unfrozen when should be)");

Found in line 374 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.operatorWhitelistedContractsLinkedListSize(operator) == linkedListLengthBefore + 1, "linked list length did not increase when it should!");

Found in line 378 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(nodeExists, "node does not exist");

Found in line 382 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(slasher.middlewareTimesLength(operator) == middlewareTimesLengthBefore + 1, "MiddlewareTimes struct not pushed to array");

Found in line 383 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(mostRecentMiddlewareTimesStructAfter.latestServeUntilBlock == serveUntilBlock, "latestServeUntilBlock not updated correctly");

Found in line 385 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(mostRecentMiddlewareTimesStructAfter.latestServeUntilBlock  == mostRecentMiddlewareTimesStructBefore.latestServeUntilBlock, "latestServeUntilBlock updated incorrectly");

Found in line 414 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.contractCanSlashOperatorUntilBlock(operator, contractAddress) == contractCanSlashOperatorUntilBefore, "contractCanSlashOperatorUntilBlock changed unexpectedly");

Found in line 439 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(!slasher.canSlash(operator, contractAddress), "improper slashing permission has been given");

Found in line 509 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.operatorWhitelistedContractsLinkedListSize(operator) == linkedListLengthBefore, "linked list length did increased inappropriately");

Found in line 513 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(nodeExists, "node does not exist");

Found in line 517 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(slasher.middlewareTimesLength(operator) == middlewareTimesLengthBefore + 1, "MiddlewareTimes struct not pushed to array");

Found in line 518 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(mostRecentMiddlewareTimesStructAfter.latestServeUntilBlock == serveUntilBlock, "latestServeUntilBlock not updated correctly");

Found in line 520 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(mostRecentMiddlewareTimesStructAfter.latestServeUntilBlock  == mostRecentMiddlewareTimesStructBefore.latestServeUntilBlock, "latestServeUntilBlock updated incorrectly");

Found in line 549 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.contractCanSlashOperatorUntilBlock(operator, contractAddress) == contractCanSlashOperatorUntilBefore, "contractCanSlashOperatorUntilBlock changed unexpectedly");

Found in line 685 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(nodeExists, "node does not exist when it should");

Found in line 695 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.operatorWhitelistedContractsLinkedListSize(operator) == linkedListLengthBefore - 1, "linked list length did not decrease when it should!");

Found in line 698 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(!nodeExists, "node exists when it should have been deleted");

Found in line 702 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(slasher.middlewareTimesLength(operator) == middlewareTimesLengthBefore + 1, "MiddlewareTimes struct not pushed to array");

Found in line 703 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(mostRecentMiddlewareTimesStructAfter.latestServeUntilBlock == serveUntilBlock, "latestServeUntilBlock not updated correctly");

Found in line 705 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

            require(mostRecentMiddlewareTimesStructAfter.latestServeUntilBlock  == mostRecentMiddlewareTimesStructBefore.latestServeUntilBlock, "latestServeUntilBlock updated incorrectly");

Found in line 732 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        require(slasher.contractCanSlashOperatorUntilBlock(operator, contractAddress) == serveUntilBlock, "contractCanSlashOperatorUntil not set correctly");

Found in line 123 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore + amount, "sharesAfter != sharesBefore + amount");

Found in line 195 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - amount_2, "sharesAfter != sharesBefore - amount");

Found in line 251 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore + shares, "sharesAfter != sharesBefore + shares");

Found in line 253 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            require(stakerStrategyListLengthAfter == stakerStrategyListLengthBefore + 1, "stakerStrategyListLengthAfter != stakerStrategyListLengthBefore + 1");

Found in line 437 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 438 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore, "nonceAfter != nonceBefore");

Found in line 486 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore + shareAmountToReturn, "sharesAfter != sharesBefore + shareAmountToReturn");

Found in line 487 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore + 1, "nonceAfter != nonceBefore + 1");

Found in line 519 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 520 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore, "nonceAfter != nonceBefore");

Found in line 552 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 553 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore, "nonceAfter != nonceBefore");

Found in line 565 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.stakerStrategyListLength(staker) != 0, "test broken in some way, length shouldn't be 0");

Found in line 592 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingBefore is true!");

Found in line 601 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is false!");

Found in line 602 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - amount, "sharesAfter != sharesBefore - amount");

Found in line 603 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore + 1, "nonceAfter != nonceBefore + 1");

Found in line 694 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingBefore is true!");

Found in line 711 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is false!");

Found in line 712 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - withdrawalAmount, "sharesAfter != sharesBefore - withdrawalAmount");

Found in line 713 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore + 1, "nonceAfter != nonceBefore + 1");

Found in line 732 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingBefore is true!");

Found in line 742 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is false!");

Found in line 743 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - amount, "sharesAfter != sharesBefore - amount");

Found in line 744 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore + 1, "nonceAfter != nonceBefore + 1");

Found in line 752 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(delegationMock.isDelegated(address(this)), "delegation mock setup failed");

Found in line 756 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(delegationMock.isDelegated(address(this)) == !undelegateIfPossible, "undelegation mock failed");

Found in line 763 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(delegationMock.isDelegated(address(this)) == !undelegateIfPossible, "undelegation mock failed");

Found in line 770 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!delegationMock.isDelegated(address(this)), "undelegation mock failed");

Found in line 788 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingBefore is true!");

Found in line 802 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is true!");

Found in line 803 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 804 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(nonceAfter == nonceBefore, "nonceAfter != nonceBefore");

Found in line 861 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore + withdrawalAmount, "sharesAfter != sharesBefore + withdrawalAmount");

Found in line 862 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 915 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 916 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore + withdrawalAmount, "balanceAfter != balanceBefore + withdrawalAmount");

Found in line 969 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 1002 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 1003 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1032 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 1033 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1150 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 1151 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1180 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 1181 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1209 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore, "sharesAfter != sharesBefore");

Found in line 1210 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1235 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore + withdrawalAmount, "sharesAfter != sharesBefore + withdrawalAmount");

Found in line 1236 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1260 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalDelayBlocks() == valueToSet, "strategyManager.withdrawalDelayBlocks() != valueToSet");

Found in line 1285 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalDelayBlocks() == valueToSet, "strategyManager.withdrawalDelayBlocks() != valueToSet");

Found in line 1340 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - uint256(withdrawalAmount), "sharesAfter != sharesBefore - uint256(withdrawalAmount)");

Found in line 1341 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore + uint256(withdrawalAmount), "balanceAfter != balanceBefore + uint256(withdrawalAmount)");

Found in line 1343 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            require(stakerStrategyListLengthAfter == stakerStrategyListLengthBefore - 1, "stakerStrategyListLengthAfter != stakerStrategyListLengthBefore - 1");

Found in line 1383 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - amount, "sharesAfter != sharesBefore - amount");

Found in line 1384 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore + amount, "balanceAfter != balanceBefore + amount");

Found in line 1385 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == 0, "sharesAfter != 0");

Found in line 1386 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(stakerStrategyListLengthAfter == stakerStrategyListLengthBefore - 1, "stakerStrategyListLengthAfter != stakerStrategyListLengthBefore - 1");

Found in line 1456 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - amount, "sharesAfter != sharesBefore - amount");

Found in line 1457 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore + amount, "balanceAfter != balanceBefore + amount");

Found in line 1595 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore + withdrawalAmount, "balanceAfter != balanceBefore + withdrawalAmount");

Found in line 1596 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is true!");

Found in line 1615 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is true!");

Found in line 1642 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1643 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is false");

Found in line 1664 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore, "balanceAfter != balanceBefore");

Found in line 1665 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalRootPending(withdrawalRoot), "withdrawalRootPendingAfter is false");

Found in line 1998 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(sharesAfter == sharesBefore - amount, "sharesAfter != sharesBefore - amount");

Found in line 1999 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(balanceAfter == balanceBefore + amount, "balanceAfter != balanceBefore + amount");

Found in line 2010 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.withdrawalDelayBlocks() == valueToSet, "strategyManager.withdrawalDelayBlocks() != valueToSet");

Found in line 2036 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(strategyManager.strategyWhitelister() == newWhitelister, "strategyManager.strategyWhitelister() != newWhitelister");

Found in line 2059 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            require(!strategyManager.strategyIsWhitelistedForDeposit(_strategy), "strategy improperly whitelisted?");

Found in line 2067 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            require(strategyManager.strategyIsWhitelistedForDeposit(strategyArray[i]), "strategy not properly whitelisted");

Found in line 2107 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                require(!strategyManager.strategyIsWhitelistedForDeposit(strategiesToRemove[i]), "strategy not properly removed from whitelist");

Found in line 2109 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                require(strategyManager.strategyIsWhitelistedForDeposit(strategiesAdded[i]), "strategy improperly removed from whitelist?");

Found in line 2216 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            require(sharesAfter == sharesBefore + shares, "sharesAfter != sharesBefore + shares");

Found in line 2217 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            require(nonceAfter == nonceBefore + 1, "nonceAfter != nonceBefore + 1");

Found in line 30 at EigenLayerContest/src/test/unit/PauserRegistryUnit.t.sol:

        require(pauserRegistry.pauser() == newPauser, "pauser not set correctly");

Found in line 40 at EigenLayerContest/src/test/unit/PauserRegistryUnit.t.sol:

        require(pauserRegistry.unpauser() == newUnpauser, "pauser not set correctly");

Found in line 102 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        require(delayedWithdrawal.amount == delayedWithdrawalAmount, "delayedWithdrawal.amount != delayedWithdrawalAmount");

Found in line 103 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        require(delayedWithdrawal.blockCreated == block.number, "delayedWithdrawal.blockCreated != block.number");

Found in line 151 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        require(userWithdrawalsBefore.delayedWithdrawals.length == delayedWithdrawalsCreated, "userWithdrawalsBefore.delayedWithdrawals.length != delayedWithdrawalsCreated");

Found in line 344 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        require(delayedWithdrawalRouter.withdrawalDelayBlocks() == valueToSet, "delayedWithdrawalRouter.withdrawalDelayBlocks() != valueToSet");

Found in line 95 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(newShares == amountToDeposit, "newShares != amountToDeposit");

Found in line 97 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(totalSharesAfter - totalSharesBefore == newShares, "totalSharesAfter - totalSharesBefore != newShares");

Found in line 116 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(newShares == amountToDeposit, "newShares != amountToDeposit");

Found in line 118 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(totalSharesAfter - totalSharesBefore == newShares, "totalSharesAfter - totalSharesBefore != newShares");

Found in line 174 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(totalSharesAfter == 0, "shares did not decrease appropriately");

Found in line 175 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(tokenBalanceAfter - tokenBalanceBefore == strategyBalanceBefore, "tokenBalanceAfter - tokenBalanceBefore != strategyBalanceBefore");

Found in line 197 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(totalSharesBefore - totalSharesAfter == sharesToWithdraw, "shares did not decrease appropriately");

Found in line 302 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(underlyingFromShares == amountSharesToQuery, "underlyingFromShares != amountSharesToQuery");

Found in line 305 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(underlyingFromSharesView == amountSharesToQuery, "underlyingFromSharesView != amountSharesToQuery");

Found in line 323 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(underlyingFromShares == expectedValueOut, "underlyingFromShares != expectedValueOut");

Found in line 326 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(underlyingFromSharesView == expectedValueOut, "underlyingFromSharesView != expectedValueOut");

Found in line 340 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(sharesFromUnderlying == amountUnderlyingToQuery, "sharesFromUnderlying != amountUnderlyingToQuery");

Found in line 343 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(sharesFromUnderlyingView == amountUnderlyingToQuery, "sharesFromUnderlyingView != amountUnderlyingToQuery");

Found in line 348 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(sharesFromUnderlying == amountUnderlyingToQuery, "sharesFromUnderlying != amountUnderlyingToQuery");

Found in line 351 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(sharesFromUnderlyingView == amountUnderlyingToQuery, "sharesFromUnderlyingView != amountUnderlyingToQuery");

Found in line 369 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(sharesFromUnderlying == expectedValueOut, "sharesFromUnderlying != expectedValueOut");

Found in line 372 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        require(sharesFromUnderlyingView == expectedValueOut, "sharesFromUnderlyingView != expectedValueOut");

Found in line 12 at EigenLayerContest/src/test/utils/SignatureCompaction.sol:

        require(err == ECDSA.RecoverError.NoError, "error in ecrecoverPacked");

Found in line 17 at EigenLayerContest/src/test/utils/SignatureCompaction.sol:

        require(s <= HALF_CURVE_ORDER, "malleable signature, s too high");

Found in line 25 at EigenLayerContest/src/test/utils/SignatureCompaction.sol:

        require(s <= HALF_CURVE_ORDER, "malleable signature, s too high");

Found in line 35 at EigenLayerContest/script/M2_Deploy.s.sol:

        require(configChainId == currentChainId, "You are on the wrong chain for this config");

Found in line 42 at EigenLayerContest/script/M2_Deploy.s.sol:

        require(initialThreshold <= initialOracleSigners.length, "invalid initialThreshold");

Found in line 53 at EigenLayerContest/script/M2_Deploy.s.sol:

        require(beaconChainOracle.owner() == communityMultisig, "beaconChainOracle owner not set correctly");

Found in line 119 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(communityMultisig != address(0), "communityMultisig address not configured correctly!");

Found in line 120 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(teamMultisig != address(0), "teamMultisig address not configured correctly!");

Found in line 341 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delegationContract.slasher() == slasher, "delegation: slasher address not set correctly");

Found in line 342 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delegationContract.strategyManager() == strategyManager, "delegation: strategyManager address not set correctly");

Found in line 344 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(strategyManagerContract.slasher() == slasher, "strategyManager: slasher address not set correctly");

Found in line 345 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(strategyManagerContract.delegation() == delegation, "strategyManager: delegation address not set correctly");

Found in line 346 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(strategyManagerContract.eigenPodManager() == eigenPodManager, "strategyManager: eigenPodManager address not set correctly");

Found in line 348 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(slasherContract.strategyManager() == strategyManager, "slasher: strategyManager not set correctly");

Found in line 349 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(slasherContract.delegation() == delegation, "slasher: delegation not set correctly");

Found in line 351 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodManagerContract.ethPOS() == ethPOSDeposit, " eigenPodManager: ethPOSDeposit contract address not set correctly");

Found in line 352 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodManagerContract.eigenPodBeacon() == eigenPodBeacon, "eigenPodManager: eigenPodBeacon contract address not set correctly");

Found in line 353 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodManagerContract.strategyManager() == strategyManager, "eigenPodManager: strategyManager contract address not set correctly");

Found in line 354 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodManagerContract.slasher() == slasher, "eigenPodManager: slasher contract address not set correctly");

Found in line 388 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(strategyManager.owner() == communityMultisig, "strategyManager: owner not set correctly");

Found in line 389 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delegation.owner() == communityMultisig, "delegation: owner not set correctly");

Found in line 390 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(slasher.owner() == communityMultisig, "slasher: owner not set correctly");

Found in line 391 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodManager.owner() == communityMultisig, "delegation: owner not set correctly");

Found in line 393 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenLayerProxyAdmin.owner() == communityMultisig, "eigenLayerProxyAdmin: owner not set correctly");

Found in line 394 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodBeacon.owner() == communityMultisig, "eigenPodBeacon: owner not set correctly");

Found in line 395 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delayedWithdrawalRouter.owner() == communityMultisig, "delayedWithdrawalRouter: owner not set correctly");

Found in line 399 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delegation.pauserRegistry() == eigenLayerPauserReg, "delegation: pauser registry not set correctly");

Found in line 400 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(strategyManager.pauserRegistry() == eigenLayerPauserReg, "strategyManager: pauser registry not set correctly");

Found in line 401 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(slasher.pauserRegistry() == eigenLayerPauserReg, "slasher: pauser registry not set correctly");

Found in line 402 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodManager.pauserRegistry() == eigenLayerPauserReg, "eigenPodManager: pauser registry not set correctly");

Found in line 403 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delayedWithdrawalRouter.pauserRegistry() == eigenLayerPauserReg, "delayedWithdrawalRouter: pauser registry not set correctly");

Found in line 405 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenLayerPauserReg.pauser() == teamMultisig, "pauserRegistry: pauser not set correctly");

Found in line 406 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenLayerPauserReg.unpauser() == communityMultisig, "pauserRegistry: unpauser not set correctly");

Found in line 409 at EigenLayerContest/script/M1_Deploy.s.sol:

            require(deployedStrategyArray[i].pauserRegistry() == eigenLayerPauserReg, "StrategyBase: pauser registry not set correctly");

Found in line 410 at EigenLayerContest/script/M1_Deploy.s.sol:

            require(deployedStrategyArray[i].paused() == 0, "StrategyBase: init paused status set incorrectly");

Found in line 423 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(strategyManager.paused() == 0, "strategyManager: init paused status set incorrectly");

Found in line 424 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(slasher.paused() == type(uint256).max, "slasher: init paused status set incorrectly");

Found in line 425 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delegation.paused() == type(uint256).max, "delegation: init paused status set incorrectly");

Found in line 426 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(eigenPodManager.paused() == 30, "eigenPodManager: init paused status set incorrectly");

Found in line 427 at EigenLayerContest/script/M1_Deploy.s.sol:

        require(delayedWithdrawalRouter.paused() == 0, "delayedWithdrawalRouter: init paused status set incorrectly");

Found in line 54 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

        require(hasRole(MINTER_ROLE, _msgSender()), "ERC20PresetMinterPauser: must have minter role to mint");

Found in line 68 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

        require(hasRole(PAUSER_ROLE, _msgSender()), "ERC20PresetMinterPauser: must have pauser role to pause");

Found in line 82 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

        require(hasRole(PAUSER_ROLE, _msgSender()), "ERC20PresetMinterPauser: must have pauser role to unpause");

Found in line 62 at EigenLayerContest/script/utils/ExistingDeploymentParser.sol:

        require(configChainId == currentChainId, "You are on the wrong chain for this config");

---

### Mitigation

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

# VULN 6

## [GAS] Use calldata instead of memory for function arguments that do not get mutated

---

### Proof of concept

Found in line 147 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function registerOperator(uint8 operatorType, BN254.G1Point memory pk, string calldata socket) external virtual {

Found in line 157 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function _registerOperator(address operator, uint8 operatorType, BN254.G1Point memory pk, string calldata socket)

Found in line 195 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function deregisterOperator(BN254.G1Point memory pkToRemove, uint32 index) external virtual returns (bool) {

Found in line 206 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function _deregisterOperator(address operator, BN254.G1Point memory pkToRemove, uint32 index) internal {

Found in line 330 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function _processApkUpdate(BN254.G1Point memory newApk) internal returns (bytes32) {

Found in line 491 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

    function _validateOperatorStake(IQuorumRegistry.OperatorStake memory opStake, uint32 referenceBlockNumber)

Found in line 40 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

    function _initialize(uint256[] memory _quorumBips) internal virtual onlyInitializing {

Found in line 31 at EigenLayerContest/src/contracts/middleware/BLSPublicKeyCompendium.sol:

    function registerBLSPublicKey(uint256 s, BN254.G1Point memory rPoint, BN254.G1Point memory pubkeyG1, BN254.G2Point memory pubkeyG2) external {

Found in line 619 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function _updateOperatorStake(address operator, bytes32 pubkeyHash, OperatorStake memory currentOperatorStake, uint256 insertAfter)

Found in line 653 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function _recordTotalStakeUpdate(OperatorStake memory _totalStake) internal {

Found in line 478 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

    function _resolve(PaymentChallenge memory challenge, address winner) internal {

Found in line 31 at EigenLayerContest/src/contracts/interfaces/IBLSPublicKeyCompendium.sol:

    function registerBLSPublicKey(uint256 s, BN254.G1Point memory rPoint, BN254.G1Point memory pubkeyG1, BN254.G2Point memory pubkeyG2) external;

Found in line 94 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

    function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature)

Found in line 76 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

    function addOracleSigners(address[] memory _oracleSigners) external onlyOwner {

Found in line 85 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

    function removeOracleSigners(address[] memory _oracleSigners) external onlyOwner {

Found in line 130 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

    function _addOracleSigners(address[] memory _oracleSigners) internal {

Found in line 93 at EigenLayerContest/src/contracts/libraries/BN254.sol:

    function negate(G1Point memory p) internal pure returns (G1Point memory) {

Found in line 12 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function concat(bytes memory _preBytes, bytes memory _postBytes) internal pure returns (bytes memory) {

Found in line 82 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function concatStorage(bytes storage _preBytes, bytes memory _postBytes) internal {

Found in line 213 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function slice(bytes memory _bytes, uint256 _start, uint256 _length) internal pure returns (bytes memory) {

Found in line 272 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toAddress(bytes memory _bytes, uint256 _start) internal pure returns (address) {

Found in line 283 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint8(bytes memory _bytes, uint256 _start) internal pure returns (uint8) {

Found in line 294 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint16(bytes memory _bytes, uint256 _start) internal pure returns (uint16) {

Found in line 305 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32) {

Found in line 316 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint64(bytes memory _bytes, uint256 _start) internal pure returns (uint64) {

Found in line 327 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint96(bytes memory _bytes, uint256 _start) internal pure returns (uint96) {

Found in line 338 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint128(bytes memory _bytes, uint256 _start) internal pure returns (uint128) {

Found in line 349 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256) {

Found in line 360 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32) {

Found in line 371 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function equal(bytes memory _preBytes, bytes memory _postBytes) internal pure returns (bool) {

Found in line 413 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function equalStorage(bytes storage _preBytes, bytes memory _postBytes) internal view returns (bool) {

Found in line 689 at EigenLayerContest/src/test/EigenPod.t.sol:

    function _testDeployAndVerifyNewEigenPod(address _podOwner, bytes memory _signature, bytes32 _depositDataRoot)

Found in line 161 at EigenLayerContest/src/test/Whitelister.t.sol:

    function testCallStakerFromNonWhitelisterAddress(address nonWhitelister, bytes memory data) external fuzzedAddress(nonWhitelister){

Found in line 170 at EigenLayerContest/src/test/Whitelister.t.sol:

    function testNonWhitelistedOperatorRegistration(BN254.G1Point memory pk, string memory socket ) external {

Found in line 50 at EigenLayerContest/src/test/mocks/IBeaconChainOracleMock.sol:

    function addOracleSigners(address[] memory _oracleSigners) external;

Found in line 57 at EigenLayerContest/src/test/mocks/IBeaconChainOracleMock.sol:

    function removeOracleSigners(address[] memory _oracleSigners) external;

Found in line 24 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    function delegateToBySignature(address /*staker*/, address /*operator*/, uint256 /*expiry*/, bytes memory /*signature*/) external {}

Found in line 42 at EigenLayerContest/src/test/mocks/Reenterer.sol:

    function prepareReturnData(bytes memory returnDataToUse) external {

Found in line 26 at EigenLayerContest/src/test/mocks/PublicKeyCompendiumMock.sol:

    function registerBLSPublicKey(uint256 s, BN254.G1Point memory rPoint, BN254.G1Point memory pubkeyG1, BN254.G2Point memory pubkeyG2) external {

Found in line 30 at EigenLayerContest/src/test/mocks/PublicKeyCompendiumMock.sol:

    function registerPublicKey(BN254.G1Point memory pk) external {

Found in line 46 at EigenLayerContest/src/test/mocks/BeaconChainOracleMock.sol:

    function addOracleSigners(address[] memory /*_oracleSigners*/) external pure {}

Found in line 48 at EigenLayerContest/src/test/mocks/BeaconChainOracleMock.sol:

    function removeOracleSigners(address[] memory /*_oracleSigners*/) external pure {}

Found in line 58 at EigenLayerContest/src/test/SigP/BeaconProxy.sol:

    function _setBeacon(address beacon, bytes memory data) internal virtual {

Found in line 2177 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    function _depositIntoStrategyWithSignature(address staker, uint256 amount, uint256 expiry, string memory expectedRevertMessage) internal {

Found in line 64 at EigenLayerContest/src/test/unit/DelegationUnit.t.sol:

    function testBadECDSASignatureExpiry(address staker, address operator, uint256 expiry, bytes memory signature) public{

Found in line 43 at EigenLayerContest/src/test/utils/Owners.sol:

    function resetOwnersConfigJson(string memory newConfig) public {

Found in line 63 at EigenLayerContest/src/test/utils/Operators.sol:

    function readUint(string memory json, uint256 index, string memory key) public returns (uint) {

Found in line 67 at EigenLayerContest/src/test/utils/Operators.sol:

    function stringToUint(string memory s) public pure returns (uint) {

Found in line 77 at EigenLayerContest/src/test/utils/Operators.sol:

    function setOperatorJsonFilePath(string memory filepath) public {

Found in line 29 at EigenLayerContest/src/test/utils/ProofParsing.sol:

    function setJSON(string memory path) public {

Found in line 27 at EigenLayerContest/script/whitelist/Staker.sol:

    function callAddress(address implementation, bytes memory data) external onlyOwner returns(bytes memory) {

Found in line 52 at EigenLayerContest/script/utils/ExistingDeploymentParser.sol:

    function _parseDeployedContracts(string memory existingDeploymentInfoPath) internal {

Found in line 8 at EigenLayerContest/script/utils/Allocator.sol:

    function allocate(IERC20 token, address[] memory recipients, uint256 amount) public {

---

### Mitigation

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.

# VULN 7

## [GAS] Cache array length outside of loop

---

### Proof of concept

Found in line 126 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 136 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 91 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 101 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {

Found in line 111 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        for (uint i = 0; i < signatures.length; i += 65) {

Found in line 128 at EigenLayerContest/src/contracts/core/Slasher.sol:

        for (uint256 i = 0; i < frozenAddresses.length;) {

Found in line 86 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {

Found in line 131 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {

Found in line 31 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i; i < stakers.length; i++) {

Found in line 429 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 453 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 477 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 494 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint256 i = 0; i < strategyArray.length; i++) {

Found in line 46 at EigenLayerContest/src/test/Slasher.t.sol:

        for (uint256 i = 0; i < accounts.length; i++) {

Found in line 70 at EigenLayerContest/src/test/utils/Operators.sol:

        for (uint i = 0; i < b.length; i++) {

Found in line 230 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < strategyTokensAndNames.length; ++i) {

Found in line 248 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < strategyTokensAndNames.length; ++i) {

Found in line 292 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < strategyTokensAndNames.length; ++i) {

Found in line 377 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < deployedStrategyArray.length; ++i) {

Found in line 408 at EigenLayerContest/script/M1_Deploy.s.sol:

        for (uint256 i = 0; i < deployedStrategyArray.length; ++i) {

Found in line 96 at EigenLayerContest/script/utils/ExistingDeploymentParser.sol:

        for (uint256 i = 0; i < strategyList.length; ++i) {

Found in line 10 at EigenLayerContest/script/utils/Allocator.sol:

        for (uint i = 0; i < recipients.length; i++) {

---

### Mitigation

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

# VULN 8

## [GAS] Use assembly to check for address(0)

---

### Proof of concept

Found in line 70 at EigenLayerContest/src/contracts/middleware/BLSPublicKeyCompendium.sol:

            pubkeyHashToOperator[pubkeyHash] == address(0),

Found in line 72 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

            address(delegationTerms[msg.sender]) == address(0),

Found in line 342 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        return (delegatedTo[staker] == address(0));

Found in line 196 at EigenLayerContest/src/test/Delegation.t.sol:

        require(delegation.delegatedTo(staker) == address(0), "undelegation unsuccessful");

Found in line 231 at EigenLayerContest/script/M1_Deploy.s.sol:

            if (strategyTokensAndNames[i].tokenAddress == address(0)) {

---

### Mitigation

Using assembly to check for the zero address can result in significant gas savings compared to using a Solidity expression, especially if the check is performed frequently or in a loop. However, it's important to note that using assembly can make the code less readable and harder to maintain, so it should be used judiciously and with caution.

# VULN 9

## [GAS] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

---

### Proof of concept

Found in line 57 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        uint32 apkHashIndex,

Found in line 74 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        uint8 _NUMBER_OF_QUORUMS,

Found in line 147 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function registerOperator(uint8 operatorType, BN254.G1Point memory pk, string calldata socket) external virtual {

Found in line 157 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function _registerOperator(address operator, uint8 operatorType, BN254.G1Point memory pk, string calldata socket)

Found in line 187 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

        emit Registration(operator, pubkeyHash, pk, uint32(_apkUpdates.length - 1), newApkHash, socket);

Found in line 195 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function deregisterOperator(BN254.G1Point memory pkToRemove, uint32 index) external virtual returns (bool) {

Found in line 206 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function _deregisterOperator(address operator, BN254.G1Point memory pkToRemove, uint32 index) internal {

Found in line 341 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

            blockNumber: uint32(block.number)

Found in line 357 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function getCorrectApkHash(uint256 index, uint32 blockNumber) external view returns (bytes32) {

Found in line 386 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    function apkUpdateBlockNumbers(uint256 index) external view returns (uint32) {

Found in line 38 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        uint32 taskNumber,

Found in line 130 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 taskNumberToConfirm,

Found in line 131 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 referenceBlockNumber,

Found in line 206 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 stakeIndex;

Found in line 252 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 stakeIndex;

Found in line 316 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 apkIndex;

Found in line 491 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

    function _validateOperatorStake(IQuorumRegistry.OperatorStake memory opStake, uint32 referenceBlockNumber)

Found in line 34 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

        uint8 _NUMBER_OF_QUORUMS

Found in line 53 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        uint32 updateBlockNumber,

Found in line 54 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        uint32 prevUpdateBlockNumber

Found in line 69 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        uint8 _NUMBER_OF_QUORUMS

Found in line 109 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function getOperatorIndex(address operator, uint32 blockNumber, uint32 index) external view returns (uint32) {

Found in line 138 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function getTotalOperators(uint32 blockNumber, uint32 index) external view returns (uint32) {

Found in line 338 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function getFromTaskNumberForOperator(address operator) external view returns (uint32) {

Found in line 343 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function numOperators() public view returns (uint32) {

Found in line 344 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        return uint32(operatorList.length);

Found in line 378 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        totalOperatorsHistory[totalOperatorsHistory.length - 1].toBlockNumber = uint32(block.number);

Found in line 381 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        _totalOperators.index = uint32(operatorList.length);

Found in line 389 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function _removeOperator(address operator, bytes32 pubkeyHash, uint32 index) internal virtual {

Found in line 391 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        uint32 updateBlockNumber = _removeOperatorStake(pubkeyHash);

Found in line 395 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number);

Found in line 401 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        uint32 latestServeUntilBlock = serviceManager.latestServeUntilBlock();

Found in line 416 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),

Found in line 424 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function _removeOperatorStake(bytes32 pubkeyHash) internal returns(uint32) {

Found in line 433 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number);

Found in line 441 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

                updateBlockNumber: uint32(block.number),

Found in line 463 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),

Found in line 474 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function _popRegistrant(uint32 index) internal returns (address swappedOperator) {

Found in line 486 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

                uint32(block.number);

Found in line 515 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            slasher.contractCanSlashOperatorUntilBlock(operator, address(serviceManager)) == type(uint32).max,

Found in line 529 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        _operatorStake.updateBlockNumber = uint32(block.number);

Found in line 534 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

                = uint32(block.number);

Found in line 541 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        operatorIndex.index = uint32(operatorList.length - 1);

Found in line 562 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),

Found in line 577 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function _registrationStakeEvaluation(address operator, uint8 operatorType)

Found in line 624 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        updatedOperatorStake.updateBlockNumber = uint32(block.number);

Found in line 637 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number);

Found in line 641 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        serviceManager.recordStakeUpdate(operator, uint32(block.number), serviceManager.latestServeUntilBlock(), insertAfter);

Found in line 647 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),

Found in line 654 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        _totalStake.updateBlockNumber = uint32(block.number);

Found in line 655 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        totalStakeHistory[totalStakeHistory.length - 1].nextUpdateBlockNumber = uint32(block.number);

Found in line 660 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    function _deregistrationCheck(address operator, uint32 index) internal view {

Found in line 30 at EigenLayerContest/src/contracts/middleware/VoteWeigherBaseStorage.sol:

    uint8 internal constant MAX_WEIGHING_FUNCTION_LENGTH = 32;

Found in line 64 at EigenLayerContest/src/contracts/middleware/VoteWeigherBaseStorage.sol:

        uint8 _NUMBER_OF_QUORUMS

Found in line 24 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

    uint8 constant internal PAUSED_NEW_PAYMENT_COMMIT = 0;

Found in line 25 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

    uint8 constant internal PAUSED_REDEEM_PAYMENT = 1;

Found in line 79 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

    event PaymentCommit(address operator, uint32 fromTaskNumber, uint32 toTaskNumber, uint256 fee);

Found in line 89 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        address indexed operator, uint32 fromTaskNumber, uint32 toTaskNumber, uint96 amount1, uint96 amount2

Found in line 175 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

    function commitPayment(uint32 toTaskNumber, uint96 amount) external onlyWhenNotPaused(PAUSED_NEW_PAYMENT_COMMIT) {

Found in line 194 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        uint32 fromTaskNumber;

Found in line 197 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        uint32 confirmAt = uint32(block.timestamp + paymentFraudproofInterval);

Found in line 325 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

            uint32(block.timestamp + paymentFraudproofInterval),

Found in line 335 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        operatorToPayment[operator].confirmAt = uint32(block.timestamp + paymentFraudproofInterval);

Found in line 366 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        uint32 fromTaskNumber = challenge.fromTaskNumber;

Found in line 367 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        uint32 toTaskNumber = challenge.toTaskNumber;

Found in line 368 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        uint32 diff = (toTaskNumber - fromTaskNumber) / 2;

Found in line 389 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        challenge.settleAt = uint32(block.timestamp + paymentFraudproofInterval);

Found in line 408 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

    function _updateStatus(address operator, uint32 diff) internal returns (bool) {

Found in line 484 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

            operatorToPayment[operator].confirmAt = uint32(block.timestamp + paymentFraudproofInterval);

Found in line 537 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

    function _taskNumber() internal view returns (uint32) {

Found in line 137 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

    function deregisterOperator(uint32 index) external virtual returns (bool) {

Found in line 147 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

    function _deregisterOperator(address operator, uint32 index) internal {

Found in line 15 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    uint32 constant disputePeriodBlocks = 1 days / 12 seconds;

Found in line 16 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    uint8 constant numZeroes = 5;

Found in line 23 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        uint32 validAfterBlock;

Found in line 26 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public taskNumber = 0;

Found in line 27 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public latestServeUntilBlock = 0;

Found in line 80 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        uint32 newLatestServeUntilBlock = uint32(block.number + disputePeriodBlocks);

Found in line 117 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        certifiedMessageMetadatas[message].validAfterBlock = type(uint32).max;

Found in line 126 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external onlyRegistry {

Found in line 131 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external onlyRegistry {

Found in line 136 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 prevElement) external onlyRegistry {

Found in line 30 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 fromTaskNumber;

Found in line 39 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 toBlockNumber;

Found in line 41 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 index;

Found in line 47 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 updateBlockNumber;

Found in line 50 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 nextUpdateBlockNumber;

Found in line 69 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

    function getFromTaskNumberForOperator(address operator) external view returns (uint32);

Found in line 135 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

    function getOperatorIndex(address operator, uint32 blockNumber, uint32 index) external view returns (uint32);

Found in line 142 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

    function getTotalOperators(uint32 blockNumber, uint32 index) external view returns (uint32);

Found in line 145 at EigenLayerContest/src/contracts/interfaces/IQuorumRegistry.sol:

    function numOperators() external view returns (uint32);

Found in line 15 at EigenLayerContest/src/contracts/interfaces/IDelayedService.sol:

    function BLOCK_STALE_MEASURE() external view returns(uint32);

Found in line 17 at EigenLayerContest/src/contracts/interfaces/IBLSRegistry.sol:

        uint32 blockNumber;

Found in line 24 at EigenLayerContest/src/contracts/interfaces/IBLSRegistry.sol:

    function getCorrectApkHash(uint256 index, uint32 blockNumber) external returns (bytes32);

Found in line 33 at EigenLayerContest/src/contracts/interfaces/IBLSRegistry.sol:

    function apkUpdateBlockNumbers(uint256 index) external view returns (uint32);

Found in line 34 at EigenLayerContest/src/contracts/interfaces/IPaymentManager.sol:

        uint32 fromTaskNumber;

Found in line 36 at EigenLayerContest/src/contracts/interfaces/IPaymentManager.sol:

        uint32 toTaskNumber;

Found in line 38 at EigenLayerContest/src/contracts/interfaces/IPaymentManager.sol:

        uint32 confirmAt;

Found in line 63 at EigenLayerContest/src/contracts/interfaces/IPaymentManager.sol:

        uint32 fromTaskNumber;

Found in line 65 at EigenLayerContest/src/contracts/interfaces/IPaymentManager.sol:

        uint32 toTaskNumber;

Found in line 71 at EigenLayerContest/src/contracts/interfaces/IPaymentManager.sol:

        uint32 settleAt; // when committed, used for fraudproof period

Found in line 112 at EigenLayerContest/src/contracts/interfaces/IPaymentManager.sol:

    function commitPayment(uint32 toTaskNumber, uint96 amount) external;

Found in line 25 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

    uint8 internal constant PAUSED_NEW_DELEGATION = 0;

Found in line 26 at EigenLayerContest/src/contracts/core/Slasher.sol:

    uint8 internal constant PAUSED_OPT_INTO_SLASHING = 0;

Found in line 27 at EigenLayerContest/src/contracts/core/Slasher.sol:

    uint8 internal constant PAUSED_FIRST_STAKE_UPDATE = 1;

Found in line 28 at EigenLayerContest/src/contracts/core/Slasher.sol:

    uint8 internal constant PAUSED_NEW_FREEZING = 2;

Found in line 39 at EigenLayerContest/src/contracts/core/Slasher.sol:

    uint32 internal constant MAX_CAN_SLASH_UNTIL = type(uint32).max;

Found in line 60 at EigenLayerContest/src/contracts/core/Slasher.sol:

    event MiddlewareTimesAdded(address operator, uint256 index, uint32 stalestUpdateBlock, uint32 latestServeUntilBlock);

Found in line 66 at EigenLayerContest/src/contracts/core/Slasher.sol:

    event SlashingAbilityRevoked(address indexed operator, address indexed contractAddress, uint32 contractCanSlashOperatorUntilBlock);

Found in line 143 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock)

Found in line 149 at EigenLayerContest/src/contracts/core/Slasher.sol:

        _recordUpdateAndAddToMiddlewareTimes(operator, uint32(block.number), serveUntilBlock);

Found in line 166 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 insertAfter)

Found in line 200 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external onlyRegisteredForService(operator) {

Found in line 202 at EigenLayerContest/src/contracts/core/Slasher.sol:

        _recordUpdateAndAddToMiddlewareTimes(operator, uint32(block.number), serveUntilBlock);

Found in line 213 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function contractCanSlashOperatorUntilBlock(address operator, address serviceContract) external view returns (uint32) {

Found in line 218 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function latestUpdateBlock(address operator, address serviceContract) external view returns (uint32) {

Found in line 270 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function canWithdraw(address operator, uint32 withdrawalStartBlock, uint256 middlewareTimesIndex) external view returns (bool) {

Found in line 291 at EigenLayerContest/src/contracts/core/Slasher.sol:

            return (uint32(block.number) > update.latestServeUntilBlock);

Found in line 302 at EigenLayerContest/src/contracts/core/Slasher.sol:

            uint32(block.number) > update.latestServeUntilBlock

Found in line 317 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function getMiddlewareTimesIndexBlock(address operator, uint32 index) external view returns (uint32) {

Found in line 322 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function getMiddlewareTimesIndexServeUntilBlock(address operator, uint32 index) external view returns (uint32) {

Found in line 346 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function getCorrectValueForInsertAfter(address operator, uint32 updateBlock) public view returns (uint256) {

Found in line 384 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function _revokeSlashingAbility(address operator, address contractAddress, uint32 serveUntilBlock) internal {

Found in line 413 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function _recordUpdateAndAddToMiddlewareTimes(address operator, uint32 updateBlock, uint32 serveUntilBlock) internal {

Found in line 445 at EigenLayerContest/src/contracts/core/Slasher.sol:

                uint32 nextMiddlewaresLeastRecentUpdateBlock = _whitelistedContractDetails[operator][_uintToAddress(nextNode)].latestUpdateBlock;

Found in line 469 at EigenLayerContest/src/contracts/core/Slasher.sol:

    function _updateMiddlewareList(address operator, uint32 updateBlock, uint256 insertAfter) internal {

Found in line 12 at EigenLayerContest/src/contracts/libraries/MiddlewareUtils.sol:

        uint32 globalDataStoreId,

Found in line 283 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint8(bytes memory _bytes, uint256 _start) internal pure returns (uint8) {

Found in line 285 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        uint8 tempUint;

Found in line 294 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint16(bytes memory _bytes, uint256 _start) internal pure returns (uint16) {

Found in line 296 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        uint16 tempUint;

Found in line 305 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

    function toUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32) {

Found in line 307 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

        uint32 tempUint;

Found in line 73 at EigenLayerContest/src/test/EigenPod.t.sol:

    uint32 PARTIAL_WITHDRAWAL_FRAUD_PROOF_PERIOD_BLOCKS = 7 days / 12 seconds;

Found in line 409 at EigenLayerContest/src/test/EigenPod.t.sol:

        validatorFields[1] = abi.encodePacked(bytes1(uint8(1)), bytes11(0), wrongWithdrawalAddress).toBytes32(0);

Found in line 9 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

    uint8 durationToInit = 2;

Found in line 19 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        uint8 operatorIndex,

Found in line 247 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

    function _testDepositStrategies(address sender, uint256 amountToDeposit, uint8 numStratsToAdd) internal {

Found in line 255 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint8 i = 0; i < numStratsToAdd; ++i) {

Found in line 267 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        for (uint8 i = 0; i < numStratsToAdd; ++i) {

Found in line 324 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

            withdrawalStartBlock: uint32(block.number)

Found in line 347 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

    function getVSfromVandS(uint8 v, bytes32 s) internal view returns(bytes32){

Found in line 422 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        uint32 withdrawalStartBlock,

Found in line 470 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        uint32 withdrawalStartBlock,

Found in line 22 at EigenLayerContest/src/test/Delegation.t.sol:

    uint32 serveUntil = 100;

Found in line 51 at EigenLayerContest/src/test/Delegation.t.sol:

            uint8 _NUMBER_OF_QUORUMS = 2;

Found in line 211 at EigenLayerContest/src/test/Delegation.t.sol:

        (uint8 v, bytes32 r, bytes32 s) = cheats.sign(PRIVATE_KEY, digestHash);

Found in line 246 at EigenLayerContest/src/test/Delegation.t.sol:

        (uint8 v, bytes32 r, bytes32 s) = cheats.sign(PRIVATE_KEY, digestHash);

Found in line 276 at EigenLayerContest/src/test/Delegation.t.sol:

        (uint8 v, bytes32 r, bytes32 s) = cheats.sign(PRIVATE_KEY, digestHash);

Found in line 287 at EigenLayerContest/src/test/Delegation.t.sol:

    function testDelegateToBySignature_WithContractWallet_NonconformingWallet(address operator, uint96 ethAmount, uint96 eigenAmount, uint8 v, bytes32 r, bytes32 s)

Found in line 315 at EigenLayerContest/src/test/Delegation.t.sol:

        uint8 v,

Found in line 336 at EigenLayerContest/src/test/Delegation.t.sol:

    function testDelegationMultipleStrategies(uint8 numStratsToAdd, address operator, address staker)

Found in line 351 at EigenLayerContest/src/test/Delegation.t.sol:

        for (uint16 i = 0; i < numStratsToAdd; ++i) {

Found in line 483 at EigenLayerContest/src/test/Delegation.t.sol:

    function _testRegisterAdditionalOperator(uint256 index, uint32 _serveUntil) internal {

Found in line 83 at EigenLayerContest/src/test/Registration.t.sol:

    function testRegisterOperator(address operator, uint32 operatorIndex, string calldata socket) public fuzzedAddress(operator){

Found in line 102 at EigenLayerContest/src/test/Registration.t.sol:

        (uint32 toBlockNumber, uint32 index) = dlReg.pubkeyHashToIndexHistory(pubkeyHash,0);

Found in line 109 at EigenLayerContest/src/test/Registration.t.sol:

    function testDeregisterOperator(address operator, uint32 operatorIndex, string calldata socket) public fuzzedAddress(operator) {

Found in line 119 at EigenLayerContest/src/test/Registration.t.sol:

        (uint32 toBlockNumber, /*uint32 index*/) = dlReg.pubkeyHashToIndexHistory(pubkeyHash,0);

Found in line 80 at EigenLayerContest/src/test/EigenLayerDeployer.t.sol:

    uint32 PARTIAL_WITHDRAWAL_FRAUD_PROOF_PERIOD_BLOCKS = 7 days / 12 seconds;

Found in line 102 at EigenLayerContest/src/test/Slasher.t.sol:

        uint32 serveUntilBlock = uint32(block.number) + 1000;

Found in line 139 at EigenLayerContest/src/test/Slasher.t.sol:

        uint32 serveUntilBlock = uint32(block.number) + 1000;

Found in line 171 at EigenLayerContest/src/test/Slasher.t.sol:

        uint32 serveUntilBlock = uint32(block.number) + 1000;

Found in line 210 at EigenLayerContest/src/test/Slasher.t.sol:

        uint32 serveUntilBlock = uint32(block.number) + 1000;

Found in line 231 at EigenLayerContest/src/test/Slasher.t.sol:

    function testOnlyRegisteredForService(address _slasher, uint32 _serveUntilBlock) public fuzzedAddress(_slasher){

Found in line 318 at EigenLayerContest/src/test/Slasher.t.sol:

        uint32 serveUntilBlock = 10;

Found in line 199 at EigenLayerContest/src/test/Whitelister.t.sol:

            dummyReg.registerOperator(operator, uint32(block.timestamp) + 3 days);

Found in line 249 at EigenLayerContest/src/test/Whitelister.t.sol:

                uint32(block.number),

Found in line 287 at EigenLayerContest/src/test/Whitelister.t.sol:

        uint32 withdrawalStartBlock,

Found in line 61 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        uint32 serveUntilBlock = 5;

Found in line 137 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            uint32 serveUntilBlock = 5;

Found in line 177 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        uint32 serveUntilBlock = 7;

Found in line 178 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        uint32 updateBlock = 3;

Found in line 235 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

    function testDepositStrategies(uint8 numStratsToAdd) public {

Found in line 358 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            withdrawalStartBlock: uint32(block.number)

Found in line 91 at EigenLayerContest/src/test/Withdrawals.t.sol:

        generalReg1.registerOperator(operator, uint32(block.timestamp) + 3 days);

Found in line 126 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.warp(uint32(block.timestamp) + 1 days);

Found in line 127 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.roll(uint32(block.timestamp) + 1 days);

Found in line 137 at EigenLayerContest/src/test/Withdrawals.t.sol:

        uint32 queuedWithdrawalBlock = uint32(block.number);

Found in line 140 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.warp(uint32(block.timestamp) + 2 days);

Found in line 141 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.roll(uint32(block.timestamp) + 2 days);

Found in line 146 at EigenLayerContest/src/test/Withdrawals.t.sol:

            cheats.warp(uint32(block.timestamp) + 4 days);

Found in line 147 at EigenLayerContest/src/test/Withdrawals.t.sol:

            cheats.roll(uint32(block.timestamp) + 4 days);

Found in line 199 at EigenLayerContest/src/test/Withdrawals.t.sol:

        generalReg1.registerOperator(operator, uint32(block.timestamp) + 5 days);

Found in line 202 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.warp(uint32(block.timestamp) + 1 days);

Found in line 203 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.roll(uint32(block.number) + 1);

Found in line 205 at EigenLayerContest/src/test/Withdrawals.t.sol:

        generalReg2.registerOperator(operator, uint32(block.timestamp) + 5 days);

Found in line 241 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.warp(uint32(block.timestamp) + 1 days);

Found in line 242 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.roll(uint32(block.number) + 1);

Found in line 252 at EigenLayerContest/src/test/Withdrawals.t.sol:

        uint32 queuedWithdrawalBlock = uint32(block.number);

Found in line 255 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.warp(uint32(block.timestamp) + 2 days);

Found in line 256 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.roll(uint32(block.number) + 2);

Found in line 260 at EigenLayerContest/src/test/Withdrawals.t.sol:

        generalReg1.propagateStakeUpdate(operator, uint32(block.number), prevElement);

Found in line 262 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.warp(uint32(block.timestamp) + 1 days);

Found in line 263 at EigenLayerContest/src/test/Withdrawals.t.sol:

        cheats.roll(uint32(block.number) + 1);

Found in line 266 at EigenLayerContest/src/test/Withdrawals.t.sol:

        generalReg2.propagateStakeUpdate(operator, uint32(block.number), prevElement);

Found in line 270 at EigenLayerContest/src/test/Withdrawals.t.sol:

            cheats.warp(uint32(block.timestamp) + 4 days);

Found in line 271 at EigenLayerContest/src/test/Withdrawals.t.sol:

            cheats.roll(uint32(block.number) + 4);

Found in line 29 at EigenLayerContest/src/test/mocks/MiddlewareRegistryMock.sol:

    function registerOperator(address operator, uint32 serveUntil) public {

Found in line 36 at EigenLayerContest/src/test/mocks/MiddlewareRegistryMock.sol:

        uint32 latestServeUntilBlock = serviceManager.latestServeUntilBlock();

Found in line 40 at EigenLayerContest/src/test/mocks/MiddlewareRegistryMock.sol:

    function propagateStakeUpdate(address operator, uint32 blockNumber, uint256 prevElement) external {

Found in line 41 at EigenLayerContest/src/test/mocks/MiddlewareRegistryMock.sol:

        uint32 latestServeUntilBlock = serviceManager.latestServeUntilBlock();

Found in line 136 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function decimals() external view returns (uint8);

Found in line 222 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function decimals() public view virtual override returns (uint8) {

Found in line 54 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function paused(uint8 /*index*/) external pure returns (bool) {

Found in line 29 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external{}

Found in line 31 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 insertAfter) external{}

Found in line 33 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external{}

Found in line 39 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function contractCanSlashOperatorUntilBlock(address operator, address serviceContract) external view returns (uint32){}

Found in line 42 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function latestUpdateBlock(address operator, address serviceContract) external view returns (uint32){}

Found in line 45 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function getCorrectValueForInsertAfter(address operator, uint32 updateBlock) external view returns (uint256){}

Found in line 47 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function canWithdraw(address /*operator*/, uint32 /*withdrawalStartBlock*/, uint256 /*middlewareTimesIndex*/) external view returns(bool) {

Found in line 66 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function getMiddlewareTimesIndexBlock(address operator, uint32 index) external view returns(uint32){}

Found in line 69 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function getMiddlewareTimesIndexServeUntilBlock(address operator, uint32 index) external view returns(uint32){}

Found in line 9 at EigenLayerContest/src/test/mocks/MiddlewareVoteWeigherMock.sol:

    uint8 _NUMBER_OF_QUORUMS = 2;

Found in line 38 at EigenLayerContest/src/test/mocks/MiddlewareVoteWeigherMock.sol:

    function registerOperator(address operator, uint32 serveUntil) public {

Found in line 45 at EigenLayerContest/src/test/mocks/MiddlewareVoteWeigherMock.sol:

        uint32 latestServeUntilBlock = serviceManager.latestServeUntilBlock();

Found in line 49 at EigenLayerContest/src/test/mocks/MiddlewareVoteWeigherMock.sol:

    function propagateStakeUpdate(address operator, uint32 blockNumber, uint256 prevElement) external {

Found in line 50 at EigenLayerContest/src/test/mocks/MiddlewareVoteWeigherMock.sol:

        uint32 serveUntilBlock = serviceManager.latestServeUntilBlock();

Found in line 18 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function taskNumber() external pure returns (uint32) {

Found in line 28 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntil) external pure {}

Found in line 31 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 prevElement) external pure {}

Found in line 34 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntil) external pure {}

Found in line 47 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function latestServeUntilBlock() external pure returns (uint32) {

Found in line 48 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

        return type(uint32).max;

Found in line 39 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function paused(uint8 index) external view returns (bool) {}

Found in line 105 at EigenLayerContest/src/test/unit/PausableUnit.t.sol:

    function testPauseSingleIndex(uint256 previousPausedStatus, uint8 indexToPause) public {

Found in line 101 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

    function testAddOracleSigners(uint8 amountSignersToAdd) public {

Found in line 169 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

    function testRemoveOracleSigners(uint8 amountSignersToAdd, uint8 amountSignersToRemove) external {

Found in line 33 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    uint8 internal constant PAUSED_OPT_INTO_SLASHING = 0;

Found in line 34 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    uint8 internal constant PAUSED_FIRST_STAKE_UPDATE = 1;

Found in line 35 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    uint8 internal constant PAUSED_NEW_FREEZING = 2;

Found in line 37 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    uint32 internal constant MAX_CAN_SLASH_UNTIL = type(uint32).max;

Found in line 71 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    uint32 contractCanSlashOperatorUntilBefore;

Found in line 120 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 121 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 122 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 withdrawalStartBlock

Found in line 167 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 168 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock

Found in line 181 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 withdrawalStartBlock = uint32(block.number) - 1;

Found in line 299 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    function testResetFrozenStatus(uint8 numberOfOperators, uint256 pseudorandomInput) external {

Found in line 340 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    function testRecordFirstStakeUpdate(address operator, address contractAddress, uint32 serveUntilBlock)

Found in line 350 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    function _testRecordFirstStakeUpdate(address operator, address contractAddress, uint32 serveUntilBlock)

Found in line 398 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

                uint32 nextMiddlewaresLeastRecentUpdateBlock = slasher.whitelistedContractDetails(operator, _uintToAddress(nextNode)).latestUpdateBlock;

Found in line 399 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

                uint32 newValue = (nextMiddlewaresLeastRecentUpdateBlock < block.number) ? nextMiddlewaresLeastRecentUpdateBlock: uint32(block.number);

Found in line 420 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock = 0;

Found in line 437 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock = 0;

Found in line 450 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock = 0;

Found in line 463 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 464 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock,

Found in line 465 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 480 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock,

Found in line 481 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 533 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

                uint32 nextMiddlewaresLeastRecentUpdateBlock = slasher.whitelistedContractDetails(operator, _uintToAddress(nextNode)).latestUpdateBlock;

Found in line 534 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

                uint32 newValue = (nextMiddlewaresLeastRecentUpdateBlock < updateBlock) ? nextMiddlewaresLeastRecentUpdateBlock: uint32(updateBlock);

Found in line 555 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 556 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock,

Found in line 557 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 573 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 580 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock = 0;

Found in line 593 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 594 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 602 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock = 0;

Found in line 618 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 625 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock = 0;

Found in line 636 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 637 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock,

Found in line 638 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 659 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 660 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock,

Found in line 661 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock,

Found in line 718 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

                uint32 nextMiddlewaresLeastRecentUpdateBlock = slasher.whitelistedContractDetails(operator, _uintToAddress(nextNode)).latestUpdateBlock;

Found in line 719 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

                uint32 newValue = (nextMiddlewaresLeastRecentUpdateBlock < block.number) ? nextMiddlewaresLeastRecentUpdateBlock: uint32(block.number);

Found in line 738 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock

Found in line 756 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock

Found in line 776 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 prevServeUntilBlock,

Found in line 777 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 updateBlock,

Found in line 784 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        uint32 serveUntilBlock = MAX_CAN_SLASH_UNTIL;

Found in line 359 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            (uint8 v, bytes32 r, bytes32 s) = cheats.sign(privateKey, digestHash);

Found in line 371 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    function testDepositIntoStrategyWithSignature_WithContractWallet_NonconformingWallet(uint256 amount, uint8 v, bytes32 r, bytes32 s) public {

Found in line 421 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            (uint8 v, bytes32 r, bytes32 s) = cheats.sign(privateKey, digestHash);

Found in line 464 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            (uint8 v, bytes32 r, bytes32 s) = cheats.sign(privateKey, digestHash);

Found in line 506 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            (uint8 v, bytes32 r, bytes32 s) = cheats.sign(privateKey, digestHash);

Found in line 537 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            (uint8 v, bytes32 r, bytes32 s) = cheats.sign(privateKey, digestHash);

Found in line 845 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                    withdrawalStartBlock: uint32(block.number),

Found in line 901 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                    withdrawalStartBlock: uint32(block.number),

Found in line 953 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                    withdrawalStartBlock: uint32(block.number),

Found in line 1085 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                    withdrawalStartBlock: uint32(block.number),

Found in line 1132 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                    withdrawalStartBlock: uint32(block.number),

Found in line 1266 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    function testCompleteQueuedWithdrawalWithNonzeroWithdrawalDelayBlocks(uint16 valueToSet) external {

Found in line 2002 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    function testSetWithdrawalDelayBlocks(uint16 valueToSet) external {

Found in line 2050 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    function testAddStrategiesToDepositWhitelist(uint8 numberOfStrategiesToAdd) public returns (IStrategy[] memory) {

Found in line 2087 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    function testRemoveStrategiesFromDepositWhitelist(uint8 numberOfStrategiesToAdd, uint8 numberOfStrategiesToRemove) external {

Found in line 2161 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                withdrawalStartBlock: uint32(block.number),

Found in line 2193 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            (uint8 v, bytes32 r, bytes32 s) = cheats.sign(privateKey, digestHash);

Found in line 40 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

    uint8 internal constant PAUSED_PAYMENT_CLAIMS = 0;

Found in line 123 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

    function testClaimDelayedWithdrawals(uint8 delayedWithdrawalsToCreate, uint8 maxNumberOfDelayedWithdrawalsToClaim, uint256 pseudorandomNumber_, address recipient, bool useOverloadedFunction)

Found in line 137 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 delayedWithdrawalsCreated;

Found in line 186 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

    function testClaimDelayedWithdrawalsSomeUnclaimable(uint8 delayedWithdrawalsToCreate, uint8 maxNumberOfDelayedWithdrawalsToClaim, bool useOverloadedFunction) external {

Found in line 256 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 delayedWithdrawalsToCreate = 0;

Found in line 257 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 maxNumberOfDelayedWithdrawalsToClaim = 0;

Found in line 263 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 delayedWithdrawalsToCreate = 0;

Found in line 264 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 maxNumberOfDelayedWithdrawalsToClaim = 2;

Found in line 270 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 delayedWithdrawalsToCreate = 2;

Found in line 271 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 maxNumberOfDelayedWithdrawalsToClaim = 0;

Found in line 276 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

    function testClaimDelayedWithdrawals_NonzeroToClaim_AttemptToClaimNonzero(uint8 maxNumberOfDelayedWithdrawalsToClaim, uint256 pseudorandomNumber_, bool useOverloadedFunction) external {

Found in line 277 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 delayedWithdrawalsToCreate = 2;

Found in line 283 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 maxNumberOfDelayedWithdrawalsToClaim = 1;

Found in line 318 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        uint8 maxNumberOfDelayedWithdrawalsToClaim = 1;

Found in line 336 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

    function testSetWithdrawalDelayBlocks(uint16 valueToSet) external {

Found in line 62 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

    function testInitialDepositCannotBeTooSmall(uint32 amountToDeposit) public {

Found in line 202 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

    function testWithdrawFailsWhenWouldResultInForbiddenTotalShareAmount(uint32 fuzzedInput) public {

Found in line 16 at EigenLayerContest/src/test/utils/SignatureCompaction.sol:

    function packSignature(bytes32 r, bytes32 s, uint8 v) internal pure returns (bytes32, bytes32) {

Found in line 24 at EigenLayerContest/src/test/utils/SignatureCompaction.sol:

    function packVS(bytes32 s, uint8 v) internal pure returns (bytes32) {

Found in line 71 at EigenLayerContest/src/test/utils/Operators.sol:

            if (uint256(uint8(b[i])) >= 48 && uint256(uint8(b[i])) <= 57) {

Found in line 72 at EigenLayerContest/src/test/utils/Operators.sol:

                result = result * 10 + (uint256(uint8(b[i])) - 48);

Found in line 87 at EigenLayerContest/script/M1_Deploy.s.sol:

    uint32 STRATEGY_MANAGER_INIT_WITHDRAWAL_DELAY_BLOCKS;

Found in line 88 at EigenLayerContest/script/M1_Deploy.s.sol:

    uint32 DELAYED_WITHDRAWAL_ROUTER_INIT_WITHDRAWAL_DELAY_BLOCKS;

Found in line 105 at EigenLayerContest/script/M1_Deploy.s.sol:

        STRATEGY_MANAGER_INIT_WITHDRAWAL_DELAY_BLOCKS = uint32(stdJson.readUint(config_data, ".strategyManager.init_withdrawal_delay_blocks"));

Found in line 106 at EigenLayerContest/script/M1_Deploy.s.sol:

        DELAYED_WITHDRAWAL_ROUTER_INIT_WITHDRAWAL_DELAY_BLOCKS = uint32(stdJson.readUint(config_data, ".strategyManager.init_withdrawal_delay_blocks"));

---

### Mitigation

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

# VULN 10

## [GAS] Public Functions to external

---

### Proof of concept

Found in line 45 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    function owner() public view override(Ownable, IServiceManager) returns (address) {

Found in line 95 at EigenLayerContest/src/contracts/strategies/StrategyWrapper.sol:

    function sharesToUnderlyingView(uint256 amountShares) public pure override returns (uint256) {

Found in line 105 at EigenLayerContest/src/contracts/strategies/StrategyWrapper.sol:

    function sharesToUnderlying(uint256 amountShares) public pure override returns (uint256) {

Found in line 34 at EigenLayerContest/src/test/Delegation.t.sol:

    function setUp() public virtual override {

Found in line 32 at EigenLayerContest/src/test/Registration.t.sol:

    function setUp() public virtual override {

Found in line 18 at EigenLayerContest/src/test/Slasher.t.sol:

    function setUp() public override {

Found in line 62 at EigenLayerContest/src/test/Whitelister.t.sol:

    function setUp() public virtual override{

Found in line 197 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function name() public view virtual override returns (string memory) {

Found in line 205 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function symbol() public view virtual override returns (string memory) {

Found in line 222 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function decimals() public view virtual override returns (uint8) {

Found in line 229 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function totalSupply() public view virtual override returns (uint256) {

Found in line 236 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function balanceOf(address account) public view virtual override returns (uint256) {

Found in line 248 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function transfer(address to, uint256 amount) public virtual override returns (bool) {

Found in line 257 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function allowance(address owner, address spender) public view virtual override returns (uint256) {

Found in line 271 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function approve(address spender, uint256 amount) public virtual override returns (bool) {

Found in line 293 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {

Found in line 53 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function totalSupply() public view virtual override returns (uint256) {

Found in line 60 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function balanceOf(address account) public view virtual override returns (uint256) {

Found in line 72 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function transfer(address to, uint256 amount) public virtual override returns (bool) {

Found in line 81 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function allowance(address owner, address spender) public view virtual override returns (uint256) {

Found in line 95 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function approve(address /*spender*/, uint256 /*amount*/) public virtual override returns (bool) {

Found in line 115 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {

Found in line 31 at EigenLayerContest/src/test/unit/DelegationUnit.t.sol:

    function setUp() override virtual public{

---

### Mitigation

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.

# VULN 11

## [GAS] <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables

---

### Proof of concept

Found in line 78 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

                res += 1;

Found in line 257 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

            _totalStake.firstQuorumStake += currentStakes.firstQuorumStake;

Found in line 258 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

            _totalStake.secondQuorumStake += currentStakes.secondQuorumStake;

Found in line 183 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

        pointer += CALLDATA_OFFSET_NonsignerPubkeys;

Found in line 225 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

                pointer += BYTE_LENGTH_NON_SIGNER_INFO;

Found in line 269 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

                pointer += BYTE_LENGTH_NON_SIGNER_INFO;

Found in line 335 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

                pointer += BYTE_LENGTH_G1_POINT;

Found in line 388 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            pointer += BYTE_LENGTH_G2_POINT;

Found in line 398 at EigenLayerContest/src/contracts/middleware/BLSSignatureChecker.sol:

            pointer += BYTE_LENGTH_G1_POINT;

Found in line 48 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

            totalQuorumBips += _quorumBips[i];

Found in line 76 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

                    weight += uint96(

Found in line 547 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        _totalStake.firstQuorumStake += _operatorStake.firstQuorumStake;

Found in line 548 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        _totalStake.secondQuorumStake += _operatorStake.secondQuorumStake;

Found in line 143 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        depositsOf[depositFor] += amount;

Found in line 185 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

            _totalStake.firstQuorumStake += currentStakes.firstQuorumStake;

Found in line 69 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        for(uint256 i = 0; i < signatures.length; i += 65) {

Found in line 73 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

            stakeSigned += registry.firstQuorumStakedByOperator(signer);

Found in line 111 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

        for (uint i = 0; i < signatures.length; i += 65) {

Found in line 57 at EigenLayerContest/src/contracts/operators/MerkleDelegationTerms.sol:

            cumulativeClaimedByStakerOfToken[msg.sender][tokensAndAmounts[i].token] += tokensAndAmounts[i].amount;

Found in line 154 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

            operatorShares[operator][strategy] += shares;

Found in line 323 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

            operatorShares[operator][strategies[i]] += shares[i];

Found in line 110 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

        stateRootVotes[blockNumber][stateRoot] += 1;

Found in line 135 at EigenLayerContest/src/contracts/pods/BeaconChainOracle.sol:

                totalOracleSigners += 1;

Found in line 239 at EigenLayerContest/src/contracts/libraries/StructuredLinkedList.sol:

            self.size += 1; // NOT: SafeMath library should be used here to increment.

Found in line 53 at EigenLayerContest/src/contracts/strategies/StrategyWrapper.sol:

        totalShares += amount;

Found in line 51 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        amountEthStaked += delegation.operatorShares(operator, wethStrat);

Found in line 52 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        amountEigenStaked += delegation.operatorShares(operator, eigenStrat);

Found in line 369 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

            _balances[to] += amount;

Found in line 391 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        _totalSupply += amount;

Found in line 394 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

            _balances[account] += amount;

Found in line 608 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

                _balances[to] += (amount - 1);

Found in line 147 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

            _balances[to] += amount;

Found in line 167 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        _totalSupply += amount;

Found in line 168 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        _balances[account] += amount;

Found in line 171 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

            _balances[account] += amount;

Found in line 168 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

        podOwnerToUnwithdrawnPaidPenalties[podOwner] += msg.value;

Found in line 143 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

                delayedWithdrawalsCreated += 1;

Found in line 176 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

            totalDelayedWithdrawalAmount += delayedWithdrawalAmounts[i];

Found in line 201 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

                delayedWithdrawalsCreated_1 += 1;

Found in line 215 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

                delayedWithdrawalsCreated_2 += 1;

Found in line 249 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

            totalDelayedWithdrawalAmount += delayedWithdrawalAmounts[i];

---

### Mitigation

When you use the += operator on a state variable, the EVM has to perform three operations: load the current value of the state variable, add the new value to it, and then store the result back in the state variable. On the other hand, when you use the = operator and then add the values separately, the EVM only needs to perform two operations: load the current value of the state variable and add the new value to it. Better use <x> = <x> + <y> For State Variables.

# VULN 12

## [GAS] Use hardcode address instead address(this)

---

### Proof of concept

Found in line 142 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        paymentToken.safeTransferFrom(msg.sender, address(this), amount);

Found in line 191 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        paymentChallengeToken.safeTransferFrom(msg.sender, address(this), paymentChallengeAmount);

Found in line 332 at EigenLayerContest/src/contracts/middleware/PaymentManager.sol:

        paymentChallengeToken.safeTransferFrom(msg.sender, address(this), challengeAmount);

Found in line 58 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

Found in line 108 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

Found in line 150 at EigenLayerContest/src/contracts/strategies/StrategyWrapper.sol:

        return IStrategyManager(strategyManager).stakerStrategyShares(user, IStrategy(address(this)));

Found in line 132 at EigenLayerContest/src/test/EigenPod.t.sol:

        address initialOwner = address(this);

Found in line 541 at EigenLayerContest/src/test/EigenPod.t.sol:

        address recipient = address(this);

Found in line 120 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        uint256 contractBalance = underlyingToken.balanceOf(address(this));

Found in line 379 at EigenLayerContest/src/test/Delegation.t.sol:

        delegation.initialize(address(this), eigenLayerPauserReg, 0);

Found in line 77 at EigenLayerContest/src/test/Registration.t.sol:

                abi.encodeWithSelector(BLSRegistry.initialize.selector, address(this), false, _quorumBips, ethStratsAndMultipliers, eigenStratsAndMultipliers)

Found in line 91 at EigenLayerContest/src/test/EigenLayerDeployer.t.sol:

    address public eigenLayerReputedMultisig = address(this);

Found in line 226 at EigenLayerContest/src/test/EigenLayerDeployer.t.sol:

            address(this)

Found in line 245 at EigenLayerContest/src/test/EigenLayerDeployer.t.sol:

            address(this)

Found in line 58 at EigenLayerContest/src/test/Slasher.t.sol:

        slasher.optIntoSlashing(address(this));

Found in line 10 at EigenLayerContest/src/test/Pausable.t.sol:

        cheats.assume(amountToDeposit <= weth.balanceOf(address(this)));

Found in line 94 at EigenLayerContest/src/test/Whitelister.t.sol:

        dummyToken.revokeRole(keccak256("MINTER_ROLE"), address(this));

Found in line 95 at EigenLayerContest/src/test/Whitelister.t.sol:

        dummyToken.revokeRole(keccak256("PAUSER_ROLE"), address(this));

Found in line 258 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            address(this)

Found in line 294 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

            address(this)

Found in line 419 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        address initOwner = address(this);

Found in line 443 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        address sender = address(this);

Found in line 539 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        cheats.deal(address(this), 1e20);

Found in line 205 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

                        abi.encode(eigenPodBeacon, abi.encodeWithSelector(IEigenPod.initialize.selector, IEigenPodManager(address(this)), msg.sender))

Found in line 225 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

                        abi.encode(eigenPodBeacon, abi.encodeWithSelector(IEigenPod.initialize.selector, IEigenPodManager(address(this)), podOwner))

Found in line 14 at EigenLayerContest/src/test/unit/BeaconChainOracleUnit.t.sol:

    address public initialBeaconChainOwner = address(this);

Found in line 53 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

    address initialOwner = address(this);

Found in line 231 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address operator = address(this);

Found in line 232 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address contractAddress = address(this);

Found in line 247 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address contractAddress = address(this);

Found in line 303 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address contractAddress = address(this);

Found in line 418 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address operator = address(this);

Found in line 419 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address contractAddress = address(this);

Found in line 435 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address operator = address(this);

Found in line 436 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address contractAddress = address(this);

Found in line 448 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address operator = address(this);

Found in line 449 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address contractAddress = address(this);

Found in line 564 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address _contractAddress = address(this);

Found in line 601 at EigenLayerContest/src/test/unit/SlasherUnit.t.sol:

        address _contractAddress = address(this);

Found in line 49 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    address initialOwner = address(this);

Found in line 128 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 138 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 153 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 166 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 184 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address overcommittedPodOwner = address(this);

Found in line 200 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 213 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 239 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        cheats.assume(amount <= token.balanceOf(address(this)));

Found in line 260 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 280 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 349 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        cheats.assume(amount <= token.balanceOf(address(this)));

Found in line 385 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        cheats.assume(amount <= token.balanceOf(address(this)));

Found in line 561 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 577 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 610 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        cheats.assume(withdrawer != address(this));

Found in line 628 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        testDepositIntoStrategySuccessfully(address(this), REQUIRED_BALANCE_WEI);

Found in line 645 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        strategyManager.queueWithdrawal(strategyIndexes, strategyArray, shareAmounts, address(this), undelegateIfPossible);

Found in line 656 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        strategyManager.queueWithdrawal(strategyIndexes, strategyArray, shareAmounts, address(this), undelegateIfPossible);

Found in line 673 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        strategyManager.queueWithdrawal(strategyIndexes, strategyArray, shareAmounts, address(this), undelegateIfPossible);

Found in line 686 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        testDepositIntoStrategySuccessfully(/*staker*/ address(this), depositAmount);

Found in line 689 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

            _setUpQueuedWithdrawalStructSingleStrat(/*staker*/ address(this), /*withdrawer*/ address(this), dummyToken, _tempStrategyStorage, withdrawalAmount);

Found in line 691 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(/*staker*/ address(this), _tempStrategyStorage);

Found in line 692 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 nonceBefore = strategyManager.numWithdrawalsQueued(/*staker*/ address(this));

Found in line 703 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

                /*withdrawer*/ address(this),

Found in line 708 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(/*staker*/ address(this), _tempStrategyStorage);

Found in line 709 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 nonceAfter = strategyManager.numWithdrawalsQueued(/*staker*/ address(this));

Found in line 721 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 751 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        delegationMock.delegateTo(address(this));

Found in line 752 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(delegationMock.isDelegated(address(this)), "delegation mock setup failed");

Found in line 756 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(delegationMock.isDelegated(address(this)) == !undelegateIfPossible, "undelegation mock failed");

Found in line 763 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(delegationMock.isDelegated(address(this)) == !undelegateIfPossible, "undelegation mock failed");

Found in line 770 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        require(!delegationMock.isDelegated(address(this)), "undelegation mock failed");

Found in line 774 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 799 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 800 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 nonceAfter = strategyManager.numWithdrawalsQueued(address(this));

Found in line 808 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 851 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 858 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 866 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 920 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 975 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 985 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 999 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1007 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1017 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1029 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1048 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1103 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1138 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1147 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1155 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1165 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1177 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1185 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1195 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1206 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1214 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1224 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesBefore = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1232 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        uint256 sharesAfter = strategyManager.stakerStrategyShares(address(this), strategy);

Found in line 1244 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1270 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1302 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        _tempStakerStorage = address(this);

Found in line 1323 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1349 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1366 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1391 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1407 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1419 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1439 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1462 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1478 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1492 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1505 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1529 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1551 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1680 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1743 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1757 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1803 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1826 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1852 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1876 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1891 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1904 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1920 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1933 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1949 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 1962 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address staker = address(this);

Found in line 1979 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address slashedAddress = address(this);

Found in line 2043 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        address newWhitelister = address(this);

Found in line 2184 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        cheats.assume(amount <= token.balanceOf(address(this)));

Found in line 43 at EigenLayerContest/src/test/unit/DelegationUnit.t.sol:

        delegationContract.initialize(address(this), eigenLayerPauserReg, 0);

Found in line 61 at EigenLayerContest/src/test/unit/DelegationUnit.t.sol:

        delegationContract.initialize(address(this), eigenLayerPauserReg, 0);

Found in line 74 at EigenLayerContest/src/test/unit/DelegationUnit.t.sol:

        delegationContract.undelegate(address(this));

Found in line 79 at EigenLayerContest/src/test/unit/DelegationUnit.t.sol:

        delegationContract.registerAsOperator(IDelegationTerms(address(this)));

Found in line 110 at EigenLayerContest/src/test/unit/DelegationUnit.t.sol:

        delegationContract.registerAsOperator(IDelegationTerms(address(this)));

Found in line 33 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

    address initialOwner = address(this);

Found in line 75 at EigenLayerContest/src/test/unit/DelayedWithdrawalRouterUnit.t.sol:

        addressIsExcludedFromFuzzedInputs[address(this)] = true;

Found in line 31 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

    address initialOwner = address(this);

Found in line 84 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(amountToDeposit <= underlyingToken.balanceOf(address(this)));

Found in line 106 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(amountToDeposit <= underlyingToken.balanceOf(address(this)));

Found in line 166 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        uint256 tokenBalanceBefore = underlyingToken.balanceOf(address(this));

Found in line 168 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), underlyingToken, sharesToWithdraw);

Found in line 171 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        uint256 tokenBalanceAfter = underlyingToken.balanceOf(address(this));

Found in line 188 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        uint256 tokenBalanceBefore = underlyingToken.balanceOf(address(this));

Found in line 191 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), underlyingToken, sharesToWithdraw);

Found in line 194 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        uint256 tokenBalanceAfter = underlyingToken.balanceOf(address(this));

Found in line 214 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), underlyingToken, sharesToWithdraw);

Found in line 231 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), underlyingToken, amountToWithdraw);

Found in line 245 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), underlyingToken, amountToWithdraw);

Found in line 256 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), IERC20(notUnderlyingToken), amountToWithdraw);

Found in line 271 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), underlyingToken, sharesToWithdraw);

Found in line 296 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        strategy.withdraw(address(this), underlyingToken, amountToWithdraw);

Found in line 311 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(amountToDeposit <= underlyingToken.balanceOf(address(this)));

Found in line 316 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(amountToTransfer <= underlyingToken.balanceOf(address(this)));

Found in line 336 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(this), underlyingToken.balanceOf(address(strategy)));

Found in line 357 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(amountToDeposit <= underlyingToken.balanceOf(address(this)));

Found in line 362 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        cheats.assume(amountToTransfer <= underlyingToken.balanceOf(address(this)));

Found in line 9 at EigenLayerContest/script/utils/Allocator.sol:

        token.transferFrom(msg.sender, address(this), recipients.length * amount);

---

### Mitigation

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.

# VULN 13

## [GAS] Do not calculate constants

---

### Proof of concept

Found in line 15 at EigenLayerContest/src/contracts/middleware/example/HashThreshold.sol:

    uint32 constant disputePeriodBlocks = 1 days / 12 seconds;

---

### Mitigation

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

# VULN 14

## [GAS] Using private rather than public for constants, saves gas

---

### Proof of concept

Found in line 47 at EigenLayerContest/src/contracts/middleware/VoteWeigherBaseStorage.sol:

    uint256 public immutable NUMBER_OF_QUORUMS;

---

### Mitigation

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

# VULN 15

## [GAS] >= costs less gas than >

---

### Proof of concept

Found in line 291 at EigenLayerContest/src/contracts/core/Slasher.sol:

            return (uint32(block.number) > update.latestServeUntilBlock);

Found in line 232 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

        return address(getPod(podOwner)).code.length > 0;

---

### Mitigation

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.

# VULN 16

## [GAS] With assembly, .call (bool success) transfer can be done gas-optimized

---

### Proof of concept

Found in EigenLayerContest/src/test/DepositWithdraw.t.sol (function testForkMainnetDepositSteth() public {):

        );



        // cheat a bunch of ETH to this address

        cheats.deal(address(this), 1e20);

        // deposit a huge amount of ETH to get ample stETH

        (bool success, bytes memory returnData) = address(steth).call{value: 1e20}("");

        require(success, "depositing stETH failed");

        returnData;



        // deploy StrategyBase contract implementation, then create upgradeable proxy that points to implementation and initialize it

        baseStrategyImplementation = new StrategyBase(strategyManager);

Found in EigenLayerContest/src/test/mocks/Reenterer.sol (function prepareReturnData(bytes memory returnDataToUse) external {):

    receive() external payable {

        // added expectrevert logic

        if (expectedRevertData.length != 0) {

            cheats.expectRevert(expectedRevertData);

        }

        (bool success, bytes memory returnData) = target.call{

            value: msgValue

        }(callData);



        if (!success) {

            assembly {

Found in EigenLayerContest/src/test/mocks/Reenterer.sol (// added fallback function that is a copy of the `receive` function):

    fallback() external payable {

        // added expectRevert logic

        if (expectedRevertData.length != 0) {

            cheats.expectRevert(expectedRevertData);

        }

        (bool success, bytes memory returnData) = target.call{

            value: msgValue

        }(callData);



        if (!success) {

            assembly {

---

### Mitigation

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

# VULN 17

## [GAS] Empty blocks should be removed or emit something

---

### Proof of concept

Found in line 9 at EigenLayerContest/certora/harnesses/StrategyManagerHarness.sol:

        {}

Found in line 8 at EigenLayerContest/certora/harnesses/SlasherHarness.sol:

    constructor(IStrategyManager _strategyManager, IDelegationManager _delegation) Slasher(_strategyManager, _delegation) {}

Found in line 8 at EigenLayerContest/certora/harnesses/DelegationManagerHarness.sol:

    constructor(IStrategyManager _strategyManager, ISlasher _slasher) DelegationManager(_strategyManager, _slasher) {}

Found in line 37 at EigenLayerContest/src/contracts/middleware/VoteWeigherBase.sol:

    {}

Found in line 52 at EigenLayerContest/src/contracts/middleware/example/ECDSARegistry.sol:

    {}

Found in line 134 at EigenLayerContest/src/contracts/operators/MerkleDelegationTerms.sol:

    {}

Found in line 143 at EigenLayerContest/src/contracts/operators/MerkleDelegationTerms.sol:

    {}

Found in line 152 at EigenLayerContest/src/contracts/operators/MerkleDelegationTerms.sol:

    {}

Found in line 457 at EigenLayerContest/src/contracts/libraries/BytesLib.sol:

                        for {} eq(add(lt(mc, end), cb), 2) {

Found in line 11 at EigenLayerContest/src/test/mocks/LiquidStakingToken.sol:

    constructor() ERC20("Wrapped Ether", "WETH") {}

Found in line 483 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function _beforeTokenTransfer(address from, address to, uint256 amount) internal virtual {}

Found in line 499 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function _afterTokenTransfer(address from, address to, uint256 amount) internal virtual {}

Found in line 579 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        {}

Found in line 256 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function _beforeTokenTransfer(address from, address to, uint256 amount) internal virtual {}

Found in line 272 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function _afterTokenTransfer(address from, address to, uint256 amount) internal virtual {}

Found in line 8 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function slasher() external view returns(ISlasher) {}

Found in line 10 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function createPod() external pure {}

Found in line 12 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function stake(bytes calldata /*pubkey*/, bytes calldata /*signature*/, bytes32 /*depositDataRoot*/) external payable {}

Found in line 14 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function restakeBeaconChainETH(address /*podOwner*/, uint256 /*amount*/) external pure {}

Found in line 16 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function recordOvercommittedBeaconChainETH(address /*podOwner*/, uint256 /*beaconChainETHStrategyIndex*/, uint256 /*amount*/) external pure {}

Found in line 18 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function withdrawRestakedBeaconChainETH(address /*podOwner*/, address /*recipient*/, uint256 /*amount*/) external pure {}

Found in line 20 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function updateBeaconChainOracle(IBeaconChainOracle /*newBeaconChainOracle*/) external pure {}

Found in line 46 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function pause(uint256 /*newPausedStatus*/) external{}

Found in line 48 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function pauseAll() external{}

Found in line 62 at EigenLayerContest/src/test/mocks/EigenPodManagerMock.sol:

    function unpause(uint256 /*newPausedStatus*/) external{}

Found in line 17 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    function registerAsOperator(IDelegationTerms /*dt*/) external {}

Found in line 24 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    function delegateToBySignature(address /*staker*/, address /*operator*/, uint256 /*expiry*/, bytes memory /*signature*/) external {}

Found in line 32 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    function delegationTerms(address /*operator*/) external view returns (IDelegationTerms) {}

Found in line 35 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    function operatorShares(address /*operator*/, IStrategy /*strategy*/) external view returns (uint256) {}

Found in line 38 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    function increaseDelegatedShares(address /*staker*/, IStrategy /*strategy*/, uint256 /*shares*/) external {}

Found in line 44 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    ) external {}

Found in line 50 at EigenLayerContest/src/test/mocks/DelegationMock.sol:

    function isNotDelegated(address /*staker*/) external pure returns (bool) {}

Found in line 14 at EigenLayerContest/src/test/mocks/ETHDepositMock.sol:

    ) external payable {}

Found in line 12 at EigenLayerContest/src/test/mocks/ERC20_SetTransferReverting_Mock.sol:

        {}

Found in line 25 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function optIntoSlashing(address contractAddress) external{}

Found in line 27 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function resetFrozenStatus(address[] calldata frozenAddresses) external{}

Found in line 29 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external{}

Found in line 31 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 insertAfter) external{}

Found in line 33 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external{}

Found in line 36 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function canSlash(address toBeSlashed, address slashingContract) external view returns (bool){}

Found in line 39 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function contractCanSlashOperatorUntilBlock(address operator, address serviceContract) external view returns (uint32){}

Found in line 42 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function latestUpdateBlock(address operator, address serviceContract) external view returns (uint32){}

Found in line 45 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function getCorrectValueForInsertAfter(address operator, uint32 updateBlock) external view returns (uint256){}

Found in line 60 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function operatorToMiddlewareTimes(address operator, uint256 arrayIndex) external view returns (MiddlewareTimes memory) {}

Found in line 63 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function middlewareTimesLength(address operator) external view returns (uint256) {}

Found in line 66 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function getMiddlewareTimesIndexBlock(address operator, uint32 index) external view returns(uint32){}

Found in line 69 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function getMiddlewareTimesIndexServeUntilBlock(address operator, uint32 index) external view returns(uint32){}

Found in line 72 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function operatorWhitelistedContractsLinkedListSize(address operator) external view returns (uint256) {}

Found in line 75 at EigenLayerContest/src/test/mocks/SlasherMock.sol:

    function operatorWhitelistedContractsLinkedListEntry(address operator, address node) external view returns (bool, uint256, uint256) {}

Found in line 39 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        returns (uint256){}

Found in line 42 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function depositBeaconChainETH(address staker, uint256 amount) external{}

Found in line 46 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        external{}

Found in line 57 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        returns (uint256 shares){}

Found in line 60 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function stakerStrategyShares(address user, IStrategy strategy) external view returns (uint256 shares){}

Found in line 66 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory){}

Found in line 69 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function stakerStrats(address staker) external view returns (IStrategy[] memory){}

Found in line 72 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function stakerStrategyListLength(address staker) external view returns (uint256){}

Found in line 82 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        external returns(bytes32){}

Found in line 91 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        external{}

Found in line 99 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        external{}

Found in line 110 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        external{}

Found in line 122 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        external{}

Found in line 130 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

        returns (bytes32){}

Found in line 133 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function beaconChainETHStrategy() external view returns (IStrategy){}

Found in line 135 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function withdrawalDelayBlocks() external view returns (uint256){}

Found in line 137 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function addStrategiesToDepositWhitelist(IStrategy[] calldata /*strategiesToWhitelist*/) external pure {}

Found in line 139 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    function removeStrategiesFromDepositWhitelist(IStrategy[] calldata /*strategiesToRemoveFromWhitelist*/) external pure {}

Found in line 17 at EigenLayerContest/src/test/mocks/MiddlewareVoteWeigherMock.sol:

    {}

Found in line 44 at EigenLayerContest/src/test/mocks/BeaconChainOracleMock.sol:

    function setThreshold(uint256 /*_threshold*/) external pure {}

Found in line 46 at EigenLayerContest/src/test/mocks/BeaconChainOracleMock.sol:

    function addOracleSigners(address[] memory /*_oracleSigners*/) external pure {}

Found in line 48 at EigenLayerContest/src/test/mocks/BeaconChainOracleMock.sol:

    function removeOracleSigners(address[] memory /*_oracleSigners*/) external pure {}

Found in line 50 at EigenLayerContest/src/test/mocks/BeaconChainOracleMock.sol:

    function voteForBeaconChainStateRoot(uint64 /*blockNumber*/, bytes32 /*stateRoot*/) external pure {}

Found in line 52 at EigenLayerContest/src/test/mocks/BeaconChainOracleMock.sol:

    function latestConfirmedOracleBlockNumber() external pure returns(uint64) {}

Found in line 28 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntil) external pure {}

Found in line 31 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 prevElement) external pure {}

Found in line 34 at EigenLayerContest/src/test/mocks/ServiceManagerMock.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntil) external pure {}

Found in line 31 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function getBeaconChainStateRoot(uint64 slot) external view returns(bytes32) {}

Found in line 33 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function pause(uint256 newPausedStatus) external {}

Found in line 35 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function pauseAll() external {}

Found in line 37 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function paused() external view returns (uint256) {}

Found in line 39 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function paused(uint8 index) external view returns (bool) {}

Found in line 41 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function pauserRegistry() external view returns (IPauserRegistry) {}

Found in line 43 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function unpause(uint256 newPausedStatus) external {}

Found in line 45 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    function ownerToPod(address podOwner) external view returns(IEigenPod) {}

---

### Mitigation

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}).

# VULN 18

## [GAS] Use double require instead of using &&

---

### Proof of concept

Found in line 49 at EigenLayerContest/src/contracts/middleware/BLSPublicKeyCompendium.sol:

        require(shouldBeZero.X == 0 && shouldBeZero.Y == 0, "BLSPublicKeyCompendium.registerBLSPublicKey: incorrect schnorr singature");

Found in line 73 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

        require(_NUMBER_OF_QUORUMS <= 2 && _NUMBER_OF_QUORUMS > 0, "RegistryBase: NUMBER_OF_QUORUMS must be less than or equal to 2 and greater than 0");

---

### Mitigation 

Using double require instead of operator && can save more gas. When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.
