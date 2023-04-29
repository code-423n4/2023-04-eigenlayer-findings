# VULN 1 

## Use != 0 instead of > 0 for unsigned integer comparison
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 75 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

                if (sharesAmount > 0) {


Found in line 73 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        require(_NUMBER_OF_QUORUMS <= 2 && _NUMBER_OF_QUORUMS > 0, "RegistryBase: NUMBER_OF_QUORUMS must be less than or equal to 2 and greater than 0");


Found in line 608 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            _operatorStake.firstQuorumStake > 0 || _operatorStake.secondQuorumStake > 0,

------------------------------------------------------------------------ 

### Mitigation 

Use != 0 instead of > 0 for unsigned integer comparison.










# VULN 2 

## Use shift Right/Left instead of division/multiplication if possible
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 368 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        uint32 diff = (toTaskNumber - fromTaskNumber) / 2;


Found in line 78 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(stakeSigned >= 666667 * uint256(totalStake) / 1000000, "Need more than 2/3 of stake to sign");


Found in line 133 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

        uint256 numNodesInLayer = leaves.length / 2;

------------------------------------------------------------------------ 

### Mitigation 

Use shift Right/Left instead of division/multiplication if possible.










# VULN 3 

## ++i/i++ should be unchecked{++i}/unchecked{i++} and ++i costs less gas than i++
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 126 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 136 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 91 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 101 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 51 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        for (uint256 i = 0; i < 10; i++) {


Found in line 88 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        taskNumber++;


Found in line 466 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {


Found in line 115 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

        for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {


Found in line 172 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {


Found in line 223 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {


Found in line 137 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

        for (uint i = 0; i < numNodesInLayer; i++) {


Found in line 145 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

            for (uint i = 0; i < numNodesInLayer; i++) {

------------------------------------------------------------------------ 

### Mitigation 

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for and while-loops. Moreover ++i costs less gas than i++, especially when its used in for-loops (--i/i-- too).










# VULN 4 

## Don’t initialize variables with default value
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 126 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 136 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 244 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        for (uint256 i = 0; i < operatorsLength;) {


Found in line 67 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

            for (uint256 i = 0; i < stratsLength;) {


Found in line 116 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

        for (uint256 i = 0; i < numStrats;) {


Found in line 151 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

        for (uint256 i = 0; i < numStrats;) {


Found in line 189 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

        for (uint256 i = 0; i < numStratsToAdd;) {


Found in line 191 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

            for (uint256 j = 0; j < (numStratsExisting + i);) {


Found in line 91 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 101 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 171 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint256 i = 0; i < operatorsLength;) {


Found in line 26 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public taskNumber = 0;


Found in line 27 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public latestServeUntilBlock = 0;


Found in line 51 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        for (uint256 i = 0; i < 10; i++) {


Found in line 68 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        uint128 stakeSigned = 0;


Found in line 69 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        for(uint256 i = 0; i < signatures.length; i += 65) {


Found in line 111 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        for (uint i = 0; i < signatures.length; i += 65) {


Found in line 185 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

            for (uint256 i = 0; i < stratsLength;) {


Found in line 321 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        for (uint256 i = 0; i < stratsLength;) {


Found in line 128 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

        for (uint256 i = 0; i < frozenAddresses.length;) {


Found in line 358 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for (uint256 i = 0; i < strategies.length;) {


Found in line 466 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {


Found in line 498 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for (uint256 i = 0; i < strategiesLength;) {


Found in line 557 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        uint256 indicesToSkipIndex = 0;


Found in line 560 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for (uint256 i = 0; i < strategiesLength;) {


Found in line 594 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for (uint256 i = 0; i < strategiesToWhitelistLength;) {


Found in line 609 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {


Found in line 724 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

            uint256 j = 0;


Found in line 780 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

            for (uint256 i = 0; i < strategiesLength;) {


Found in line 797 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

            for (uint256 i = 0; i < strategiesLength;) {


Found in line 861 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for (uint256 i = 0; i < strategiesLength;) {


Found in line 115 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

        for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {


Found in line 138 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

        uint256 amountToSend = 0;


Found in line 141 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

        uint256 i = 0;


Found in line 86 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {


Found in line 131 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {


Found in line 172 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {


Found in line 223 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        for (uint256 i = 0; i < 2; i++) {


Found in line 267 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        uint256 beta = 0;


Found in line 268 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        uint256 y = 0;


Found in line 137 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

        for (uint i = 0; i < numNodesInLayer; i++) {


Found in line 145 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

            for (uint i = 0; i < numNodesInLayer; i++) {


Found in line 133 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {


Found in line 143 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {


Found in line 153 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {


Found in line 163 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {

------------------------------------------------------------------------ 

### Mitigation 

In such cases, initializing the variables with default values would be unnecessary and can be considered a waste of gas. Additionally, initializing variables with default values can sometimes lead to unnecessary storage operations, which can increase gas costs. For example, if you have a large array of variables, initializing them all with default values can result in a lot of unnecessary storage writes, which can increase the gas costs of your contract.










# VULN 5 

## Use Custom Errors
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 22 at 2023-04-eigenlayer/src/contracts/permissions/PauserRegistry.sol:

        require(msg.sender == unpauser, "msg.sender is not permissioned as unpauser");


Found in line 42 at 2023-04-eigenlayer/src/contracts/permissions/PauserRegistry.sol:

        require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");


Found in line 48 at 2023-04-eigenlayer/src/contracts/permissions/PauserRegistry.sol:

        require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");


Found in line 33 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

        require(msg.sender == pauserRegistry.pauser(), "msg.sender is not permissioned as pauser");


Found in line 38 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

        require(msg.sender == pauserRegistry.unpauser(), "msg.sender is not permissioned as unpauser");


Found in line 44 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

        require(_paused == 0, "Pausable: contract is paused");


Found in line 50 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

        require(!paused(index), "Pausable: index is paused");


Found in line 73 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

        require((_paused & newPausedStatus) == _paused, "Pausable.pause: invalid attempt to unpause functionality");


Found in line 94 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

        require(((~_paused) & (~newPausedStatus)) == (~_paused), "Pausable.unpause: invalid attempt to pause functionality");


Found in line 67 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        require(operatorWhitelister == msg.sender, "BLSRegistry.onlyOperatorWhitelister: not operatorWhitelister");


Found in line 161 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

            require(whitelisted[operator], "BLSRegistry._registerOperator: not whitelisted");


Found in line 170 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        require(pubkeyHash != ZERO_PK_HASH, "BLSRegistry._registerOperator: Cannot register with 0x0 public key");


Found in line 242 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        require(operatorsLength == prevElements.length, "BLSRegistry.updateStakes: prevElement is not the same length as operators");


Found in line 348 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        require(_operatorWhitelister != address(0), "BLSRegistry.initialize: cannot set operatorWhitelister to zero address");


Found in line 359 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        require(blockNumber >= _apkUpdates[index].blockNumber, "BLSRegistry.getCorrectApkHash: index too recent");


Found in line 364 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

            require(blockNumber < _apkUpdates[index + 1].blockNumber, "BLSRegistry.getCorrectApkHash: Not latest valid apk update");


Found in line 280 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            require(uint256(pubkeyHash) > uint256(pubkeyHashes[i - 1]), "BLSSignatureChecker.checkSignatures: Pubkey hashes must be in ascending order");


Found in line 308 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            require(success, "BLSSignatureChecker.checkSignatures: non signer addition failed");


Found in line 363 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            require(success, "BLSSignatureChecker.checkSignatures: aggregate non signer addition failed");


Found in line 412 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: aggregate signer public key random shift failed");


Found in line 422 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: aggregate signer public key and signature addition failed");


Found in line 434 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: generator random shift failed");


Found in line 441 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: generator random shift and G1 hash addition failed");


Found in line 466 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        require(success, "BLSSignatureChecker.checkSignatures: pairing precompile call failed");


Found in line 468 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        require(input[0] == 1, "BLSSignatureChecker.checkSignatures: Pairing unsuccessful");


Found in line 496 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        require(opStake.updateBlockNumber <= referenceBlockNumber, "Provided stake index is too early");


Found in line 26 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

        require(msg.sender == serviceManager.owner(), "onlyServiceManagerOwner");


Found in line 52 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

        require(totalQuorumBips == MAX_BIPS, "VoteWeigherBase._initialize: totalQuorumBips != MAX_BIPS");


Found in line 114 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

        require(indicesToRemove.length == numStrats, "VoteWeigherBase.removeStrategiesConsideredAndWeights: input length mismatch");


Found in line 49 at 2023-04-eigenlayer/src/contracts/middleware/BLSPublicKeyCompendium.sol:

        require(shouldBeZero.X == 0 && shouldBeZero.Y == 0, "BLSPublicKeyCompendium.registerBLSPublicKey: incorrect schnorr singature");


Found in line 63 at 2023-04-eigenlayer/src/contracts/middleware/BLSPublicKeyCompendium.sol:

        require(pubkeyHash != ZERO_PK_HASH, "BLSPublicKeyCompendium.registerBLSPublicKey: operator attempting to register the zero public key");


Found in line 73 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        require(_NUMBER_OF_QUORUMS <= 2 && _NUMBER_OF_QUORUMS > 0, "RegistryBase: NUMBER_OF_QUORUMS must be less than or equal to 2 and greater than 0");


Found in line 666 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        require(operator == operatorList[index], "RegistryBase._deregistrationCheck: Incorrect index supplied");


Found in line 67 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBaseStorage.sol:

        require(_NUMBER_OF_QUORUMS != 0, "VoteWeigherBaseStorage.constructor: _NUMBER_OF_QUORUMS == 0");


Found in line 100 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        require(msg.sender == address(serviceManager), "onlyServiceManager");


Found in line 106 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        require(msg.sender == address(registry), "onlyRegistry");


Found in line 112 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        require(msg.sender == serviceManager.owner(), "onlyServiceManagerOwner");


Found in line 182 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        require(toTaskNumber <= _taskNumber(), "PaymentManager.commitPayment: Cannot claim future payments");


Found in line 211 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        require(fromTaskNumber < toTaskNumber, "invalid payment range");


Found in line 39 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        require(operatorWhitelister == msg.sender, "BLSRegistry.onlyOperatorWhitelister: not operatorWhitelister");


Found in line 121 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

            require(whitelisted[operator], "BLSRegistry._registerOperator: not whitelisted");


Found in line 169 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        require(operatorsLength == prevElements.length, "BLSRegistry.updateStakes: prevElement is not the same length as operators");


Found in line 197 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        require(_operatorWhitelister != address(0), "BLSRegistry.initialize: cannot set operatorWhitelister to zero address");


Found in line 33 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(msg.sender == address(registry), "Only registry can call this function");


Found in line 65 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(certifiedMessageMetadatas[message].validAfterBlock == 0, "Message already certified");


Found in line 67 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(msg.sender == tx.origin, "EOA must call this function");


Found in line 72 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

            require(registry.isActiveOperator(signer), "Signer is not an active operator");


Found in line 78 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(stakeSigned >= 666667 * uint256(totalStake) / 1000000, "Need more than 2/3 of stake to sign");


Found in line 105 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(certifiedMessageMetadata.validAfterBlock > block.number, "Dispute period has passed");


Found in line 107 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(certifiedMessageMetadata.signaturesHash == keccak256(signatures), "Signatures do not match");


Found in line 109 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        require(decaHash(message) >> (256 - numZeroes) == 0, "Message does not hash to enough zeroes");


Found in line 67 at 2023-04-eigenlayer/src/contracts/operators/MerkleDelegationTerms.sol:

        require(height <= MAX_HEIGHT, "MerkleDelegationTerms.postMerkleRoot: height input too large");


Found in line 95 at 2023-04-eigenlayer/src/contracts/operators/MerkleDelegationTerms.sol:

        require(proof.length == 32 * merkleRoots[rootIndex].height, "MerkleDelegationTerms.proveEarningsAndWithdraw: incorrect proof length");


Found in line 35 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        require(msg.sender == address(strategyManager), "onlyStrategyManager");


Found in line 97 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        require(expiry >= block.timestamp, "DelegationManager.delegateToBySignature: delegation signature expired");


Found in line 137 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        require(!isOperator(staker), "DelegationManager.undelegate: operators cannot undelegate from themselves");


Found in line 309 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        require(isNotDelegated(staker), "DelegationManager._delegate: staker has existing delegation");


Found in line 311 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        require(!slasher.isFrozen(operator), "DelegationManager._delegate: cannot delegate to a frozen operator");


Found in line 105 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

        require(delegation.isOperator(msg.sender), "Slasher.optIntoSlashing: msg.sender is not a registered operator");


Found in line 171 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

        require(updateBlock <= block.number, "Slasher.recordStakeUpdate: cannot provide update for future block");


Found in line 385 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

        require(serveUntilBlock != MAX_CAN_SLASH_UNTIL, "Slasher._revokeSlashingAbility: serveUntilBlock time must be limited");


Found in line 105 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(slasher.isFrozen(staker), "StrategyManager.onlyFrozen: staker has not been frozen");


Found in line 110 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(address(eigenPodManager) == msg.sender, "StrategyManager.onlyEigenPodManager: not the eigenPodManager");


Found in line 115 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(msg.sender == strategyWhitelister, "StrategyManager.onlyStrategyWhitelister: not the strategyWhitelister");


Found in line 120 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(strategyIsWhitelistedForDeposit[strategy], "StrategyManager.onlyStrategiesWhitelistedForDeposit: strategy not whitelisted");


Found in line 342 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(strategies.length == shares.length, "StrategyManager.queueWithdrawal: input length mismatch");


Found in line 343 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");


Found in line 495 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(tokens.length == strategies.length, "StrategyManager.slashShares: input length mismatch");


Found in line 542 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.slashQueuedWithdrawal: input length mismatch");


Found in line 631 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");


Found in line 632 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(shares != 0, "StrategyManager._addShares: shares should not be zero!");


Found in line 684 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");


Found in line 685 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(shareAmount != 0, "StrategyManager._removeShares: shareAmount should not be zero!");


Found in line 690 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(shareAmount <= userShares, "StrategyManager._removeShares: shareAmount too high");


Found in line 736 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

            require(j != stratsLength, "StrategyManager._removeStrategyFromStakerStrategyList: strategy not found");


Found in line 778 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

            require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.completeQueuedWithdrawal: input length mismatch");


Found in line 812 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(stakerStrategyList[depositor].length == 0, "StrategyManager._undelegate: depositor has active deposits");


Found in line 840 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        require(_withdrawalDelayBlocks <= MAX_WITHDRAWAL_DELAY_BLOCKS, "StrategyManager.setWithdrawalDelay: _withdrawalDelayBlocks too high");


Found in line 40 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

        require(address(eigenPodManager.getPod(podOwner)) == msg.sender, "DelayedWithdrawalRouter.onlyEigenPod: not podOwner's EigenPod");


Found in line 45 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

        require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");


Found in line 167 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

        require(newValue <= MAX_WITHDRAWAL_DELAY_BLOCKS, "DelayedWithdrawalRouter._setWithdrawalDelayBlocks: newValue too large");


Found in line 53 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        require(isOracleSigner[msg.sender], "BeaconChainOracle.onlyOracleSigner: Not an oracle signer");


Found in line 105 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        require(!hasVoted[blockNumber][msg.sender], "BeaconChainOracle.voteForBeaconChainStateRoot: Signer has already voted");


Found in line 106 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        require(beaconStateRootAtBlockNumber[blockNumber] == bytes32(0), "BeaconChainOracle.voteForBeaconChainStateRoot: State root already confirmed");


Found in line 124 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        require(_threshold >= MINIMUM_THRESHOLD, "BeaconChainOracle._setThreshold: cannot set threshold below MINIMUM_THRESHOLD");


Found in line 100 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(msg.sender == address(eigenPodManager), "EigenPod.onlyEigenPodManager: not eigenPodManager");


Found in line 105 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(msg.sender == podOwner, "EigenPod.onlyEigenPodOwner: not podOwner");


Found in line 110 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(!eigenPodManager.slasher().isFrozen(podOwner), "EigenPod.onlyNotFrozen: pod owner is frozen");


Found in line 115 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(!hasRestaked, "EigenPod.hasNeverRestaked: restaking is enabled");


Found in line 132 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(!IPausable(address(eigenPodManager)).paused(index), "EigenPod.onlyWhenNotPaused: index is paused in EigenPodManager");


Found in line 147 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0, "EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a whole number of gwei");


Found in line 153 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");


Found in line 160 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(msg.value == 32 ether, "EigenPod.stake: must initially stake for any validator with 32 ether");


Found in line 252 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(validatorStatus[validatorIndex] == VALIDATOR_STATUS.ACTIVE, "EigenPod.verifyOvercommittedStake: Validator not active");


Found in line 270 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

            require(slashedStatus == 1, "EigenPod.verifyOvercommittedStake: Validator must be slashed to be overcommitted");


Found in line 423 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        require(!provenPartialWithdrawal[validatorIndex][withdrawalHappenedSlot], "EigenPod._processPartialWithdrawal: partial withdrawal has already been proven for this slot");


Found in line 67 at 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol:

        require(address(ownerToPod[podOwner]) == msg.sender, "EigenPodManager.onlyEigenPod: not a pod");


Found in line 72 at 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol:

        require(msg.sender == address(strategyManager), "EigenPodManager.onlyStrategyManager: not strategyManager");


Found in line 100 at 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol:

        require(!hasPod(msg.sender), "EigenPodManager.createPod: Sender already has a pod");


Found in line 217 at 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol:

        require(stateRoot != bytes32(0), "EigenPodManager.getBeaconChainStateRoot: state root at blockNumber not yet finalized");


Found in line 126 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        require(success, "ec-add-failed");


Found in line 152 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        require(success, "ec-mul-failed");


Found in line 202 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        require(success, "pairing-opcode-failed");


Found in line 321 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

        require(success, "BN254.expMod: call failure");


Found in line 214 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_length + 31 >= _length, "slice_overflow");


Found in line 215 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + _length, "slice_outOfBounds");


Found in line 273 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 20, "toAddress_outOfBounds");


Found in line 284 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 1, "toUint8_outOfBounds");


Found in line 295 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 2, "toUint16_outOfBounds");


Found in line 306 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 4, "toUint32_outOfBounds");


Found in line 317 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 8, "toUint64_outOfBounds");


Found in line 328 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 12, "toUint96_outOfBounds");


Found in line 339 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 16, "toUint128_outOfBounds");


Found in line 350 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 32, "toUint256_outOfBounds");


Found in line 361 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        require(_bytes.length >= _start + 32, "toBytes32_outOfBounds");


Found in line 199 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(validatorFields.length == 2**VALIDATOR_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyValidatorFields: Validator fields has incorrect length");


Found in line 205 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(proof.length == 32 * ((VALIDATOR_TREE_HEIGHT + 1) + BEACON_STATE_FIELD_TREE_HEIGHT), "BeaconChainProofs.verifyValidatorFields: Proof has incorrect length");


Found in line 211 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(Merkle.verifyInclusionSha256(proof, beaconStateRoot, validatorRoot, index), "BeaconChainProofs.verifyValidatorFields: Invalid merkle proof");


Found in line 227 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(proof.length == 32 * ((BALANCE_TREE_HEIGHT + 1) + BEACON_STATE_FIELD_TREE_HEIGHT), "BeaconChainProofs.verifyValidatorBalance: Proof has incorrect length");


Found in line 236 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(Merkle.verifyInclusionSha256(proof, beaconStateRoot, balanceRoot, balanceIndex), "BeaconChainProofs.verifyValidatorBalance: Invalid merkle proof");


Found in line 250 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(withdrawalFields.length == 2**WITHDRAWAL_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalFields has incorrect length");


Found in line 252 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(proofs.blockHeaderRootIndex < 2**BLOCK_ROOTS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: blockRootIndex is too large");


Found in line 253 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(proofs.withdrawalIndex < 2**WITHDRAWALS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalIndex is too large");


Found in line 273 at 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol:

        require(Merkle.verifyInclusionSha256(proofs.slotProof, proofs.blockHeaderRoot, proofs.slotRoot, SLOT_INDEX), "BeaconChainProofs.verifyWithdrawalProofs: Invalid slot merkle proof");


Found in line 30 at 2023-04-eigenlayer/src/contracts/strategies/StrategyWrapper.sol:

        require(msg.sender == address(strategyManager), "StrategyWrapper.onlyStrategyManager");


Found in line 52 at 2023-04-eigenlayer/src/contracts/strategies/StrategyWrapper.sol:

        require(token == underlyingToken, "StrategyWrapper.deposit: Can only deposit underlyingToken");


Found in line 69 at 2023-04-eigenlayer/src/contracts/strategies/StrategyWrapper.sol:

        require(token == underlyingToken, "StrategyWrapper.withdraw: Can only withdraw the strategy token");


Found in line 41 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

        require(msg.sender == address(strategyManager), "StrategyBase.onlyStrategyManager");


Found in line 86 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

        require(token == underlyingToken, "StrategyBase.deposit: Can only deposit underlyingToken");


Found in line 104 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

        require(newShares != 0, "StrategyBase.deposit: newShares cannot be zero");


Found in line 128 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

        require(token == underlyingToken, "StrategyBase.withdraw: Can only withdraw the strategy token");

------------------------------------------------------------------------ 

### Mitigation 

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.










# VULN 6 

## Use calldata instead of memory for function arguments that do not get mutated
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 147 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function registerOperator(uint8 operatorType, BN254.G1Point memory pk, string calldata socket) external virtual {


Found in line 157 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function _registerOperator(address operator, uint8 operatorType, BN254.G1Point memory pk, string calldata socket)


Found in line 195 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function deregisterOperator(BN254.G1Point memory pkToRemove, uint32 index) external virtual returns (bool) {


Found in line 206 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function _deregisterOperator(address operator, BN254.G1Point memory pkToRemove, uint32 index) internal {


Found in line 330 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function _processApkUpdate(BN254.G1Point memory newApk) internal returns (bytes32) {


Found in line 491 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

    function _validateOperatorStake(IQuorumRegistry.OperatorStake memory opStake, uint32 referenceBlockNumber)


Found in line 40 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

    function _initialize(uint256[] memory _quorumBips) internal virtual onlyInitializing {


Found in line 31 at 2023-04-eigenlayer/src/contracts/middleware/BLSPublicKeyCompendium.sol:

    function registerBLSPublicKey(uint256 s, BN254.G1Point memory rPoint, BN254.G1Point memory pubkeyG1, BN254.G2Point memory pubkeyG2) external {


Found in line 619 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function _updateOperatorStake(address operator, bytes32 pubkeyHash, OperatorStake memory currentOperatorStake, uint256 insertAfter)


Found in line 653 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function _recordTotalStakeUpdate(OperatorStake memory _totalStake) internal {


Found in line 478 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

    function _resolve(PaymentChallenge memory challenge, address winner) internal {


Found in line 31 at 2023-04-eigenlayer/src/contracts/interfaces/IBLSPublicKeyCompendium.sol:

    function registerBLSPublicKey(uint256 s, BN254.G1Point memory rPoint, BN254.G1Point memory pubkeyG1, BN254.G2Point memory pubkeyG2) external;


Found in line 38 at 2023-04-eigenlayer/src/contracts/interfaces/IDelegationManager.sol:

    function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;


Found in line 46 at 2023-04-eigenlayer/src/contracts/interfaces/IBeaconChainOracle.sol:

    function addOracleSigners(address[] memory _oracleSigners) external;


Found in line 53 at 2023-04-eigenlayer/src/contracts/interfaces/IBeaconChainOracle.sol:

    function removeOracleSigners(address[] memory _oracleSigners) external;


Found in line 94 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

    function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature)


Found in line 876 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

    function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {


Found in line 76 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

    function addOracleSigners(address[] memory _oracleSigners) external onlyOwner {


Found in line 85 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

    function removeOracleSigners(address[] memory _oracleSigners) external onlyOwner {


Found in line 130 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

    function _addOracleSigners(address[] memory _oracleSigners) internal {


Found in line 93 at 2023-04-eigenlayer/src/contracts/libraries/BN254.sol:

    function negate(G1Point memory p) internal pure returns (G1Point memory) {


Found in line 12 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function concat(bytes memory _preBytes, bytes memory _postBytes) internal pure returns (bytes memory) {


Found in line 82 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function concatStorage(bytes storage _preBytes, bytes memory _postBytes) internal {


Found in line 213 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function slice(bytes memory _bytes, uint256 _start, uint256 _length) internal pure returns (bytes memory) {


Found in line 272 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toAddress(bytes memory _bytes, uint256 _start) internal pure returns (address) {


Found in line 283 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint8(bytes memory _bytes, uint256 _start) internal pure returns (uint8) {


Found in line 294 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint16(bytes memory _bytes, uint256 _start) internal pure returns (uint16) {


Found in line 305 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32) {


Found in line 316 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint64(bytes memory _bytes, uint256 _start) internal pure returns (uint64) {


Found in line 327 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint96(bytes memory _bytes, uint256 _start) internal pure returns (uint96) {


Found in line 338 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint128(bytes memory _bytes, uint256 _start) internal pure returns (uint128) {


Found in line 349 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256) {


Found in line 360 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32) {


Found in line 371 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function equal(bytes memory _preBytes, bytes memory _postBytes) internal pure returns (bool) {


Found in line 413 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function equalStorage(bytes storage _preBytes, bytes memory _postBytes) internal view returns (bool) {


Found in line 48 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

    function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {


Found in line 99 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

    function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

------------------------------------------------------------------------ 

### Mitigation 

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.










# VULN 7 

## Cache array length outside of loop
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 126 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 136 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 91 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 101 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

        for (uint i = 0; i < operators.length; i++) {


Found in line 111 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        for (uint i = 0; i < signatures.length; i += 65) {


Found in line 128 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

        for (uint256 i = 0; i < frozenAddresses.length;) {


Found in line 358 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        for (uint256 i = 0; i < strategies.length;) {


Found in line 86 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {


Found in line 131 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        for (uint256 i = 0; i < _oracleSigners.length;) {


Found in line 50 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

        for (uint256 i = 32; i <= proof.length; i+=32) {


Found in line 101 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

        for (uint256 i = 32; i <= proof.length; i+=32) {

------------------------------------------------------------------------ 

### Mitigation 

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).










# VULN 8 

## Use assembly to check for address(0)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 57 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

            address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),


Found in line 70 at 2023-04-eigenlayer/src/contracts/middleware/BLSPublicKeyCompendium.sol:

            pubkeyHashToOperator[pubkeyHash] == address(0),


Found in line 72 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

            address(delegationTerms[msg.sender]) == address(0),


Found in line 342 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        return (delegatedTo[staker] == address(0));


Found in line 114 at 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol:

        if(address(pod) == address(0)) {


Found in line 196 at 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol:

        if (address(pod) == address(0)) {

------------------------------------------------------------------------ 

### Mitigation 

Using assembly to check for the zero address can result in significant gas savings compared to using a Solidity expression, especially if the check is performed frequently or in a loop. However, it's important to note that using assembly can make the code less readable and harder to maintain, so it should be used judiciously and with caution.










# VULN 9 

## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 49 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

    modifier onlyWhenNotPaused(uint8 index) {


Found in line 105 at 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol:

    function paused(uint8 index) public view virtual returns (bool) {


Found in line 57 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        uint32 apkHashIndex,


Found in line 74 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        uint8 _NUMBER_OF_QUORUMS,


Found in line 147 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function registerOperator(uint8 operatorType, BN254.G1Point memory pk, string calldata socket) external virtual {


Found in line 157 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function _registerOperator(address operator, uint8 operatorType, BN254.G1Point memory pk, string calldata socket)


Found in line 187 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

        emit Registration(operator, pubkeyHash, pk, uint32(_apkUpdates.length - 1), newApkHash, socket);


Found in line 195 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function deregisterOperator(BN254.G1Point memory pkToRemove, uint32 index) external virtual returns (bool) {


Found in line 206 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function _deregisterOperator(address operator, BN254.G1Point memory pkToRemove, uint32 index) internal {


Found in line 341 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

            blockNumber: uint32(block.number)


Found in line 357 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function getCorrectApkHash(uint256 index, uint32 blockNumber) external view returns (bytes32) {


Found in line 386 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

    function apkUpdateBlockNumbers(uint256 index) external view returns (uint32) {


Found in line 38 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        uint32 taskNumber,


Found in line 130 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 taskNumberToConfirm,


Found in line 131 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 referenceBlockNumber,


Found in line 206 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 stakeIndex;


Found in line 252 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 stakeIndex;


Found in line 316 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            uint32 apkIndex;


Found in line 491 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

    function _validateOperatorStake(IQuorumRegistry.OperatorStake memory opStake, uint32 referenceBlockNumber)


Found in line 34 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

        uint8 _NUMBER_OF_QUORUMS


Found in line 53 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        uint32 updateBlockNumber,


Found in line 54 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        uint32 prevUpdateBlockNumber


Found in line 69 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        uint8 _NUMBER_OF_QUORUMS


Found in line 109 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function getOperatorIndex(address operator, uint32 blockNumber, uint32 index) external view returns (uint32) {


Found in line 138 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function getTotalOperators(uint32 blockNumber, uint32 index) external view returns (uint32) {


Found in line 338 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function getFromTaskNumberForOperator(address operator) external view returns (uint32) {


Found in line 343 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function numOperators() public view returns (uint32) {


Found in line 344 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        return uint32(operatorList.length);


Found in line 378 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        totalOperatorsHistory[totalOperatorsHistory.length - 1].toBlockNumber = uint32(block.number);


Found in line 381 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        _totalOperators.index = uint32(operatorList.length);


Found in line 389 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function _removeOperator(address operator, bytes32 pubkeyHash, uint32 index) internal virtual {


Found in line 391 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        uint32 updateBlockNumber = _removeOperatorStake(pubkeyHash);


Found in line 395 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number);


Found in line 401 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        uint32 latestServeUntilBlock = serviceManager.latestServeUntilBlock();


Found in line 416 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),


Found in line 424 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function _removeOperatorStake(bytes32 pubkeyHash) internal returns(uint32) {


Found in line 433 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number);


Found in line 441 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

                updateBlockNumber: uint32(block.number),


Found in line 463 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),


Found in line 474 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function _popRegistrant(uint32 index) internal returns (address swappedOperator) {


Found in line 486 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

                uint32(block.number);


Found in line 515 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            slasher.contractCanSlashOperatorUntilBlock(operator, address(serviceManager)) == type(uint32).max,


Found in line 529 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        _operatorStake.updateBlockNumber = uint32(block.number);


Found in line 534 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

                = uint32(block.number);


Found in line 541 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        operatorIndex.index = uint32(operatorList.length - 1);


Found in line 562 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),


Found in line 577 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function _registrationStakeEvaluation(address operator, uint8 operatorType)


Found in line 624 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        updatedOperatorStake.updateBlockNumber = uint32(block.number);


Found in line 637 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number);


Found in line 641 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        serviceManager.recordStakeUpdate(operator, uint32(block.number), serviceManager.latestServeUntilBlock(), insertAfter);


Found in line 647 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

            uint32(block.number),


Found in line 654 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        _totalStake.updateBlockNumber = uint32(block.number);


Found in line 655 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        totalStakeHistory[totalStakeHistory.length - 1].nextUpdateBlockNumber = uint32(block.number);


Found in line 660 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

    function _deregistrationCheck(address operator, uint32 index) internal view {


Found in line 30 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBaseStorage.sol:

    uint8 internal constant MAX_WEIGHING_FUNCTION_LENGTH = 32;


Found in line 64 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBaseStorage.sol:

        uint8 _NUMBER_OF_QUORUMS


Found in line 24 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

    uint8 constant internal PAUSED_NEW_PAYMENT_COMMIT = 0;


Found in line 25 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

    uint8 constant internal PAUSED_REDEEM_PAYMENT = 1;


Found in line 79 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

    event PaymentCommit(address operator, uint32 fromTaskNumber, uint32 toTaskNumber, uint256 fee);


Found in line 89 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        address indexed operator, uint32 fromTaskNumber, uint32 toTaskNumber, uint96 amount1, uint96 amount2


Found in line 175 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

    function commitPayment(uint32 toTaskNumber, uint96 amount) external onlyWhenNotPaused(PAUSED_NEW_PAYMENT_COMMIT) {


Found in line 194 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        uint32 fromTaskNumber;


Found in line 197 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        uint32 confirmAt = uint32(block.timestamp + paymentFraudproofInterval);


Found in line 325 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

            uint32(block.timestamp + paymentFraudproofInterval),


Found in line 335 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        operatorToPayment[operator].confirmAt = uint32(block.timestamp + paymentFraudproofInterval);


Found in line 366 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        uint32 fromTaskNumber = challenge.fromTaskNumber;


Found in line 367 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        uint32 toTaskNumber = challenge.toTaskNumber;


Found in line 368 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        uint32 diff = (toTaskNumber - fromTaskNumber) / 2;


Found in line 389 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        challenge.settleAt = uint32(block.timestamp + paymentFraudproofInterval);


Found in line 408 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

    function _updateStatus(address operator, uint32 diff) internal returns (bool) {


Found in line 484 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

            operatorToPayment[operator].confirmAt = uint32(block.timestamp + paymentFraudproofInterval);


Found in line 537 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

    function _taskNumber() internal view returns (uint32) {


Found in line 137 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

    function deregisterOperator(uint32 index) external virtual returns (bool) {


Found in line 147 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

    function _deregisterOperator(address operator, uint32 index) internal {


Found in line 15 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    uint32 constant disputePeriodBlocks = 1 days / 12 seconds;


Found in line 16 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    uint8 constant numZeroes = 5;


Found in line 23 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        uint32 validAfterBlock;


Found in line 26 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public taskNumber = 0;


Found in line 27 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    uint32 public latestServeUntilBlock = 0;


Found in line 80 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        uint32 newLatestServeUntilBlock = uint32(block.number + disputePeriodBlocks);


Found in line 117 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        certifiedMessageMetadatas[message].validAfterBlock = type(uint32).max;


Found in line 126 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external onlyRegistry {


Found in line 131 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external onlyRegistry {


Found in line 136 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 prevElement) external onlyRegistry {


Found in line 30 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 fromTaskNumber;


Found in line 39 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 toBlockNumber;


Found in line 41 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 index;


Found in line 47 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 updateBlockNumber;


Found in line 50 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

        uint32 nextUpdateBlockNumber;


Found in line 69 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

    function getFromTaskNumberForOperator(address operator) external view returns (uint32);


Found in line 135 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

    function getOperatorIndex(address operator, uint32 blockNumber, uint32 index) external view returns (uint32);


Found in line 142 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

    function getTotalOperators(uint32 blockNumber, uint32 index) external view returns (uint32);


Found in line 145 at 2023-04-eigenlayer/src/contracts/interfaces/IQuorumRegistry.sol:

    function numOperators() external view returns (uint32);


Found in line 15 at 2023-04-eigenlayer/src/contracts/interfaces/IDelayedService.sol:

    function BLOCK_STALE_MEASURE() external view returns(uint32);    


Found in line 17 at 2023-04-eigenlayer/src/contracts/interfaces/IBLSRegistry.sol:

        uint32 blockNumber;


Found in line 24 at 2023-04-eigenlayer/src/contracts/interfaces/IBLSRegistry.sol:

    function getCorrectApkHash(uint256 index, uint32 blockNumber) external returns (bytes32);


Found in line 33 at 2023-04-eigenlayer/src/contracts/interfaces/IBLSRegistry.sol:

    function apkUpdateBlockNumbers(uint256 index) external view returns (uint32);


Found in line 31 at 2023-04-eigenlayer/src/contracts/interfaces/IStrategyManager.sol:

        uint32 withdrawalStartBlock;


Found in line 33 at 2023-04-eigenlayer/src/contracts/interfaces/IEigenPod.sol:

        uint32 creationBlockNumber;


Found in line 35 at 2023-04-eigenlayer/src/contracts/interfaces/IEigenPod.sol:

        uint32 fraudproofPeriodEndBlockNumber;


Found in line 13 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

        uint32 stalestUpdateBlock;


Found in line 15 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

        uint32 latestServeUntilBlock;


Found in line 21 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

        uint32 contractCanSlashOperatorUntilBlock;


Found in line 23 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

        uint32 latestUpdateBlock;


Found in line 53 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external;


Found in line 65 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 insertAfter) external;


Found in line 75 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external;


Found in line 90 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function contractCanSlashOperatorUntilBlock(address operator, address serviceContract) external view returns (uint32);


Found in line 93 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function latestUpdateBlock(address operator, address serviceContract) external view returns (uint32);


Found in line 96 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function getCorrectValueForInsertAfter(address operator, uint32 updateBlock) external view returns (uint256);


Found in line 110 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function canWithdraw(address operator, uint32 withdrawalStartBlock, uint256 middlewareTimesIndex) external returns(bool);


Found in line 127 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function getMiddlewareTimesIndexBlock(address operator, uint32 index) external view returns(uint32);


Found in line 130 at 2023-04-eigenlayer/src/contracts/interfaces/ISlasher.sol:

    function getMiddlewareTimesIndexServeUntilBlock(address operator, uint32 index) external view returns(uint32);


Found in line 8 at 2023-04-eigenlayer/src/contracts/interfaces/IDelayedWithdrawalRouter.sol:

        uint32 blockCreated;


Found in line 34 at 2023-04-eigenlayer/src/contracts/interfaces/IPaymentManager.sol:

        uint32 fromTaskNumber;


Found in line 36 at 2023-04-eigenlayer/src/contracts/interfaces/IPaymentManager.sol:

        uint32 toTaskNumber;


Found in line 38 at 2023-04-eigenlayer/src/contracts/interfaces/IPaymentManager.sol:

        uint32 confirmAt;


Found in line 63 at 2023-04-eigenlayer/src/contracts/interfaces/IPaymentManager.sol:

        uint32 fromTaskNumber;


Found in line 65 at 2023-04-eigenlayer/src/contracts/interfaces/IPaymentManager.sol:

        uint32 toTaskNumber;


Found in line 71 at 2023-04-eigenlayer/src/contracts/interfaces/IPaymentManager.sol:

        uint32 settleAt; // when committed, used for fraudproof period


Found in line 112 at 2023-04-eigenlayer/src/contracts/interfaces/IPaymentManager.sol:

    function commitPayment(uint32 toTaskNumber, uint96 amount) external;


Found in line 39 at 2023-04-eigenlayer/src/contracts/interfaces/IPausable.sol:

    function paused(uint8 index) external view returns (bool);


Found in line 13 at 2023-04-eigenlayer/src/contracts/interfaces/IServiceManager.sol:

    function taskNumber() external view returns (uint32);


Found in line 19 at 2023-04-eigenlayer/src/contracts/interfaces/IServiceManager.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external;


Found in line 22 at 2023-04-eigenlayer/src/contracts/interfaces/IServiceManager.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 prevElement) external;


Found in line 25 at 2023-04-eigenlayer/src/contracts/interfaces/IServiceManager.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external;


Found in line 28 at 2023-04-eigenlayer/src/contracts/interfaces/IServiceManager.sol:

    function latestServeUntilBlock() external view returns (uint32);


Found in line 25 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

    uint8 internal constant PAUSED_NEW_DELEGATION = 0;


Found in line 28 at 2023-04-eigenlayer/src/contracts/core/StrategyManagerStorage.sol:

    uint8 internal constant MAX_STAKER_STRATEGY_LIST_LENGTH = 32;


Found in line 26 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    uint8 internal constant PAUSED_OPT_INTO_SLASHING = 0;


Found in line 27 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    uint8 internal constant PAUSED_FIRST_STAKE_UPDATE = 1;


Found in line 28 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    uint8 internal constant PAUSED_NEW_FREEZING = 2;


Found in line 39 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    uint32 internal constant MAX_CAN_SLASH_UNTIL = type(uint32).max;


Found in line 60 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    event MiddlewareTimesAdded(address operator, uint256 index, uint32 stalestUpdateBlock, uint32 latestServeUntilBlock);


Found in line 66 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    event SlashingAbilityRevoked(address indexed operator, address indexed contractAddress, uint32 contractCanSlashOperatorUntilBlock);


Found in line 143 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) 


Found in line 149 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

        _recordUpdateAndAddToMiddlewareTimes(operator, uint32(block.number), serveUntilBlock);


Found in line 166 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 insertAfter) 


Found in line 200 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external onlyRegisteredForService(operator) {


Found in line 202 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

        _recordUpdateAndAddToMiddlewareTimes(operator, uint32(block.number), serveUntilBlock);


Found in line 213 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function contractCanSlashOperatorUntilBlock(address operator, address serviceContract) external view returns (uint32) {


Found in line 218 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function latestUpdateBlock(address operator, address serviceContract) external view returns (uint32) {


Found in line 270 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function canWithdraw(address operator, uint32 withdrawalStartBlock, uint256 middlewareTimesIndex) external view returns (bool) {


Found in line 291 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

            return (uint32(block.number) > update.latestServeUntilBlock);


Found in line 302 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

            uint32(block.number) > update.latestServeUntilBlock


Found in line 317 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function getMiddlewareTimesIndexBlock(address operator, uint32 index) external view returns (uint32) {


Found in line 322 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function getMiddlewareTimesIndexServeUntilBlock(address operator, uint32 index) external view returns (uint32) {


Found in line 346 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function getCorrectValueForInsertAfter(address operator, uint32 updateBlock) public view returns (uint256) {


Found in line 384 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function _revokeSlashingAbility(address operator, address contractAddress, uint32 serveUntilBlock) internal {


Found in line 413 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function _recordUpdateAndAddToMiddlewareTimes(address operator, uint32 updateBlock, uint32 serveUntilBlock) internal {


Found in line 445 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

                uint32 nextMiddlewaresLeastRecentUpdateBlock = _whitelistedContractDetails[operator][_uintToAddress(nextNode)].latestUpdateBlock;


Found in line 469 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

    function _updateMiddlewareList(address operator, uint32 updateBlock, uint256 insertAfter) internal {


Found in line 38 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

    uint8 internal constant PAUSED_DEPOSITS = 0;


Found in line 40 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

    uint8 internal constant PAUSED_WITHDRAWALS = 1;


Found in line 405 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

                withdrawalStartBlock: uint32(block.number),


Found in line 16 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

    uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;


Found in line 64 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

                blockCreated: uint32(block.number)


Found in line 131 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

    modifier onlyWhenNotPaused(uint8 index) {


Found in line 455 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        mostRecentWithdrawalBlockNumber = uint32(block.number);


Found in line 461 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));


Found in line 10 at 2023-04-eigenlayer/src/contracts/pods/EigenPodPausingConstants.sol:

    uint8 internal constant PAUSED_NEW_EIGENPODS = 0;


Found in line 12 at 2023-04-eigenlayer/src/contracts/pods/EigenPodPausingConstants.sol:

    uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;


Found in line 15 at 2023-04-eigenlayer/src/contracts/pods/EigenPodPausingConstants.sol:

    uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;


Found in line 17 at 2023-04-eigenlayer/src/contracts/pods/EigenPodPausingConstants.sol:

    uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;


Found in line 19 at 2023-04-eigenlayer/src/contracts/pods/EigenPodPausingConstants.sol:

    uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;


Found in line 12 at 2023-04-eigenlayer/src/contracts/libraries/MiddlewareUtils.sol:

        uint32 globalDataStoreId,


Found in line 283 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint8(bytes memory _bytes, uint256 _start) internal pure returns (uint8) {


Found in line 285 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        uint8 tempUint;


Found in line 294 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint16(bytes memory _bytes, uint256 _start) internal pure returns (uint16) {


Found in line 296 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        uint16 tempUint;


Found in line 305 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

    function toUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32) {


Found in line 307 at 2023-04-eigenlayer/src/contracts/libraries/BytesLib.sol:

        uint32 tempUint;


Found in line 22 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

    uint8 internal constant PAUSED_DEPOSITS = 0;


Found in line 23 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

    uint8 internal constant PAUSED_WITHDRAWALS = 1;

------------------------------------------------------------------------ 

### Mitigation 

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.










# VULN 10 

## Public Functions to external
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 45 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    function owner() public view override(Ownable, IServiceManager) returns (address) {


Found in line 95 at 2023-04-eigenlayer/src/contracts/strategies/StrategyWrapper.sol:

    function sharesToUnderlyingView(uint256 amountShares) public pure override returns (uint256) {


Found in line 105 at 2023-04-eigenlayer/src/contracts/strategies/StrategyWrapper.sol:

    function sharesToUnderlying(uint256 amountShares) public pure override returns (uint256) {


Found in line 172 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

    function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {


Found in line 186 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

    function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {

------------------------------------------------------------------------ 

### Mitigation 

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.










# VULN 11 

## <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 257 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

            _totalStake.firstQuorumStake += currentStakes.firstQuorumStake;


Found in line 258 at 2023-04-eigenlayer/src/contracts/middleware/BLSRegistry.sol:

            _totalStake.secondQuorumStake += currentStakes.secondQuorumStake;


Found in line 183 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

        pointer += CALLDATA_OFFSET_NonsignerPubkeys;


Found in line 225 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

                pointer += BYTE_LENGTH_NON_SIGNER_INFO;


Found in line 269 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

                pointer += BYTE_LENGTH_NON_SIGNER_INFO;


Found in line 335 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

                pointer += BYTE_LENGTH_G1_POINT;


Found in line 388 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            pointer += BYTE_LENGTH_G2_POINT;


Found in line 398 at 2023-04-eigenlayer/src/contracts/middleware/BLSSignatureChecker.sol:

            pointer += BYTE_LENGTH_G1_POINT;


Found in line 48 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

            totalQuorumBips += _quorumBips[i];


Found in line 76 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBase.sol:

                    weight += uint96(


Found in line 547 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        _totalStake.firstQuorumStake += _operatorStake.firstQuorumStake;


Found in line 548 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        _totalStake.secondQuorumStake += _operatorStake.secondQuorumStake;


Found in line 143 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        depositsOf[depositFor] += amount;


Found in line 185 at 2023-04-eigenlayer/src/contracts/middleware/example/ECDSARegistry.sol:

            _totalStake.firstQuorumStake += currentStakes.firstQuorumStake;


Found in line 69 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        for(uint256 i = 0; i < signatures.length; i += 65) {


Found in line 73 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

            stakeSigned += registry.firstQuorumStakedByOperator(signer);


Found in line 111 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

        for (uint i = 0; i < signatures.length; i += 65) {


Found in line 57 at 2023-04-eigenlayer/src/contracts/operators/MerkleDelegationTerms.sol:

            cumulativeClaimedByStakerOfToken[msg.sender][tokensAndAmounts[i].token] += tokensAndAmounts[i].amount;


Found in line 154 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

            operatorShares[operator][strategy] += shares;


Found in line 323 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

            operatorShares[operator][strategies[i]] += shares[i];


Found in line 192 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

            beaconChainETHSharesToDecrementOnWithdrawal[overcommittedPodOwner] += debt;


Found in line 644 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        stakerStrategyShares[depositor][strategy] += shares;


Found in line 150 at 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol:

            amountToSend += delayedWithdrawal.amount;


Found in line 110 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

        stateRootVotes[blockNumber][stateRoot] += 1;


Found in line 135 at 2023-04-eigenlayer/src/contracts/pods/BeaconChainOracle.sol:

                totalOracleSigners += 1;


Found in line 377 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

                restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;


Found in line 380 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

                restakedExecutionLayerGwei += withdrawalAmountGwei;


Found in line 391 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

                restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;


Found in line 399 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

                restakedExecutionLayerGwei += withdrawalAmountGwei;


Found in line 239 at 2023-04-eigenlayer/src/contracts/libraries/StructuredLinkedList.sol:

            self.size += 1; // NOT: SafeMath library should be used here to increment.


Found in line 50 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

        for (uint256 i = 32; i <= proof.length; i+=32) {


Found in line 101 at 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol:

        for (uint256 i = 32; i <= proof.length; i+=32) {


Found in line 53 at 2023-04-eigenlayer/src/contracts/strategies/StrategyWrapper.sol:

        totalShares += amount;

------------------------------------------------------------------------ 

### Mitigation 

When you use the += operator on a state variable, the EVM has to perform three operations: load the current value of the state variable, add the new value to it, and then store the result back in the state variable. On the other hand, when you use the = operator and then add the values separately, the EVM only needs to perform two operations: load the current value of the state variable and add the new value to it. Better use <x> = <x> + <y> For State Variables.














# VULN 12

## Use hardcode address instead address(this)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 142 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        paymentToken.safeTransferFrom(msg.sender, address(this), amount);


Found in line 191 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        paymentChallengeToken.safeTransferFrom(msg.sender, address(this), paymentChallengeAmount);


Found in line 332 at 2023-04-eigenlayer/src/contracts/middleware/PaymentManager.sol:

        paymentChallengeToken.safeTransferFrom(msg.sender, address(this), challengeAmount);


Found in line 58 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));


Found in line 108 at 2023-04-eigenlayer/src/contracts/core/DelegationManager.sol:

            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));


Found in line 150 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));


Found in line 276 at 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol:

            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));


Found in line 456 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        _sendETH(podOwner, address(this).balance);


Found in line 461 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

        return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));


Found in line 150 at 2023-04-eigenlayer/src/contracts/strategies/StrategyWrapper.sol:

        return IStrategyManager(strategyManager).stakerStrategyShares(user, IStrategy(address(this)));


Found in line 236 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

        return strategyManager.stakerStrategyShares(user, IStrategy(address(this)));


Found in line 242 at 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol:

        return underlyingToken.balanceOf(address(this));

------------------------------------------------------------------------ 

### Mitigation 

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.










# VULN 14 

## Do not calculate constants
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 15 at 2023-04-eigenlayer/src/contracts/middleware/example/HashThreshold.sol:

    uint32 constant disputePeriodBlocks = 1 days / 12 seconds;

------------------------------------------------------------------------ 

### Mitigation 

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.










# VULN 15 

## Using private rather than public for constants, saves gas
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 47 at 2023-04-eigenlayer/src/contracts/middleware/VoteWeigherBaseStorage.sol:

    uint256 public immutable NUMBER_OF_QUORUMS;


Found in line 56 at 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol:

    uint256 public immutable REQUIRED_BALANCE_WEI;

------------------------------------------------------------------------ 

### Mitigation 

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.










# VULN 16 

## >= costs less gas than >
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 291 at 2023-04-eigenlayer/src/contracts/core/Slasher.sol:

            return (uint32(block.number) > update.latestServeUntilBlock);

------------------------------------------------------------------------ 

### Mitigation 

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.










# VULN 17 

## Use double require instead of using &&
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 49 at 2023-04-eigenlayer/src/contracts/middleware/BLSPublicKeyCompendium.sol:

        require(shouldBeZero.X == 0 && shouldBeZero.Y == 0, "BLSPublicKeyCompendium.registerBLSPublicKey: incorrect schnorr singature");



Found in line 73 at 2023-04-eigenlayer/src/contracts/middleware/RegistryBase.sol:

        require(_NUMBER_OF_QUORUMS <= 2 && _NUMBER_OF_QUORUMS > 0, "RegistryBase: NUMBER_OF_QUORUMS must be less than or equal to 2 and greater than 0");


------------------------------------------------------------------------ 

### Mitigation 

Using double require instead of operator && can save more gas. When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.
