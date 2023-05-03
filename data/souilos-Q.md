# VULN 1

## [LOW] Keccak Constant values should used to immutable rather than constant

---

### Proof of concept

Found in line 28 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

Found in line 29 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

    bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

---

### Mitigation

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

# VULN 2

## [LOW] Empty receive()/payable fallback() function does not authenticate requests

---

### Proof of concept

Found in line 29 at EigenLayerContest/src/test/mocks/LiquidStakingToken.sol:

    receive() external payable {

Found in line 46 at EigenLayerContest/src/test/mocks/Reenterer.sol:

    receive() external payable {

---

### Mitigation

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))).Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas. Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.

# VULN 3

## [LOW] Use Ownable2Step rather than Ownable

---

### Proof of concept

Found in line 23 at EigenLayerContest/src/contracts/core/DelegationManager.sol:

contract DelegationManager is Initializable, OwnableUpgradeable, Pausable, DelegationManagerStorage {

Found in line 21 at EigenLayerContest/src/contracts/core/Slasher.sol:

contract Slasher is Initializable, OwnableUpgradeable, ISlasher, Pausable {

Found in line 19 at EigenLayerContest/src/test/mocks/StrategyManagerMock.sol:

    OwnableUpgradeable,

Found in line 30 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

contract EigenPodManagerNEW is Initializable, OwnableUpgradeable, IEigenPodManager {

---

### Mitigation

Ownable2Step and Ownable2StepUpgradeable prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.

# VULN 4

## [LOW] Keccak Constant values should used to immutable rather than constant

---

### Proof of concept

Found in line 28 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

Found in line 29 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

    bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

---

### Mitigation

By using immutable variables, the Keccak256 hash is computed at contract deployment time rather than at compilation time. This means that the value can be updated if the algorithm changes in a future compiler version, without breaking backward compatibility. Additionally, it provides better gas optimization, as the Keccak256 hash is computed only once at contract deployment instead of every time the variable is accessed during execution.

# VULN 5

## [LOW] Use .call instead of .transfer to send ether

---

### Proof of concept

Found in line 33 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

            eigenToken.transfer(stakers[i], amountEigenToDeposit);

Found in line 34 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

            weth.transfer(stakers[i], amountEthToDeposit);

Found in line 128 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

            underlyingToken.transfer(sender, amountToDeposit);

Found in line 321 at EigenLayerContest/src/test/Whitelister.t.sol:

        whiteLister.transfer(staker, address(dummyToken), receiver, AMOUNT);

Found in line 378 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        weth.transfer(attacker,2 ether);

Found in line 379 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        weth.transfer(user,2 ether);

Found in line 395 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        weth.transfer(address(wethStrat),1 ether);

Found in line 425 at EigenLayerContest/src/test/DepositWithdraw.t.sol:

        underlyingToken.transfer(sender, 1000);

Found in line 22 at EigenLayerContest/src/test/mocks/LiquidStakingToken.sol:

        payable(msg.sender).transfer(_amount);

Found in line 65 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(strategy), amountToDeposit);

Found in line 89 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(strategy), amountToDeposit);

Found in line 110 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(strategy), amountToDeposit);

Found in line 128 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(strategy), amountToDeposit);

Found in line 140 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(strategy), amountToDeposit);

Found in line 317 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(strategy), amountToTransfer);

Found in line 336 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(this), underlyingToken.balanceOf(address(strategy)));

Found in line 363 at EigenLayerContest/src/test/unit/StrategyBaseUnit.t.sol:

        underlyingToken.transfer(address(strategy), amountToTransfer);

Found in line 60 at EigenLayerContest/script/Allocate.s.sol:

        weth.transfer(recipientAddr, wethAmount);

Found in line 61 at EigenLayerContest/script/Allocate.s.sol:

        payable(recipientAddr).transfer(1 ether);

Found in line 11 at EigenLayerContest/script/utils/Allocator.sol:

            token.transfer(recipients[i], amount);

---

### Mitigation

.transfer will relay 2300 gas and .call will relay all the gas. If the receive/fallback function from the recipient proxy contract has complex logic, using .transfer will fail, causing integration issues.Replace .transfer with .call. Note that the result of .call need to be checked.

# VULN 6

## [LOW] Use the safe variant and ERC721.mint

---

### Proof of concept

Found in line 15 at EigenLayerContest/src/test/mocks/LiquidStakingToken.sol:

        _mint(msg.sender, msg.value);

Found in line 386 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

    function _mint(address account, uint256 amount) internal virtual {

Found in line 568 at EigenLayerContest/src/test/mocks/ERC20_OneWeiFeeOnTransfer.sol:

        _mint(owner, initialSupply);

Found in line 47 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        _mint(msg.sender, initSupply);

Found in line 116 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

        _mint(from, amount);

Found in line 164 at EigenLayerContest/src/test/mocks/ERC20Mock.sol:

    function _mint(address account, uint256 amount) internal virtual {

Found in line 55 at EigenLayerContest/script/whitelist/ERC20PresetMinterPauser.sol:

        _mint(to, amount);

---

### Mitigation

.mint won’t check if the recipient is able to receive the NFT. If an incorrect address is passed, it will result in a silent failure and loss of asset. OpenZeppelin recommendation is to use the safe variant of \_mint. Replace \_mint() with \_safeMint().

# VULN 7

## [LOW] Open TODOs

---

### Proof of concept

Found in line 269 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    //TODO: The subgraph doesnt like uint256[4][] argument here. Figure this out laters

Found in line 270 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    // // TODO: de-dupe code copied from `updateStakes`, if reasonably possible

Found in line 307 at EigenLayerContest/src/contracts/middleware/BLSRegistry.sol:

    //             // TODO: optimize better if possible? right now this pushes an APK update for each operator removed.

Found in line 20 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

    // TODO: set these on initialization

Found in line 569 at EigenLayerContest/src/contracts/middleware/RegistryBase.sol:

     * TODO: critique: "Currently only `_registrationStakeEvaluation` uses the `uint8 registrantType` input -- we should **EITHER** store this

Found in line 46 at EigenLayerContest/src/contracts/operators/MerkleDelegationTerms.sol:

    // TODO: more events?

Found in line 738 at EigenLayerContest/src/test/EigenPod.t.sol:

            // TODO: make this an input

Found in line 154 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

// TODO: reimplement with queued withdrawals

Found in line 274 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

            // TODO: perhaps remove this is we can. seems brittle if we don't track the number of strategies somewhere

Found in line 337 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

        // TODO: check with 'undelegateIfPossible' = false, rather than just true

Found in line 526 at EigenLayerContest/src/test/EigenLayerTestHelper.t.sol:

            // TODO: make this an input

Found in line 95 at EigenLayerContest/src/test/EigenLayerDeployer.t.sol:

    // addresses excluded from fuzzing due to abnormal behavior. TODO: @Sidu28 define this better and give it a clearer name

Found in line 26 at EigenLayerContest/src/test/Pausable.t.sol:

        // TODO: write this to work with completing a queued withdrawal

Found in line 48 at EigenLayerContest/src/test/SigP/EigenPodManagerNEW.sol:

    //TODO: change this to constant in prod

Found in line 748 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

    // TODO: set up delegation for the following three tests and check afterwords

Found in line 971 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        // TODO: make EigenPodManagerMock do something so we can verify that it gets called appropriately?

Found in line 1617 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        // TODO: add to EigenPodManager mock so it appropriately checks the call to eigenPodManager.withdrawRestakedBeaconChainETH

Found in line 20 at EigenLayerContest/script/whitelist/Whitelister.sol:

    //TODO: change before deploy

Found in line 30 at EigenLayerContest/script/whitelist/Whitelister.sol:

    //TODO: Deploy ERC20PresetMinterPauser and a corresponding StrategyBase for it

Found in line 31 at EigenLayerContest/script/whitelist/Whitelister.sol:

    //TODO: Transfer ownership of Whitelister to multisig after deployment

Found in line 32 at EigenLayerContest/script/whitelist/Whitelister.sol:

    //TODO: Give mint/admin/pauser permssions of whitelistToken to Whitelister and multisig after deployment

Found in line 33 at EigenLayerContest/script/whitelist/Whitelister.sol:

    //TODO: Give up mint/admin/pauser permssions of whitelistToken for deployer

---

### Mitigation

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

# VULN 8

## [LOW] Use safeTransferOwnership instead of transferOwnership function

---

### Proof of concept

Found in line 85 at EigenLayerContest/src/test/Whitelister.t.sol:

        whiteLister.transferOwnership(theMultiSig);

Found in line 1560 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        strategyManager.transferOwnership(address(reenterer));

Found in line 1696 at EigenLayerContest/src/test/unit/StrategyManagerUnit.t.sol:

        strategyManager.transferOwnership(address(reenterer));

Found in line 260 at EigenLayerContest/script/M1_Deploy.s.sol:

        eigenLayerProxyAdmin.transferOwnership(communityMultisig);

Found in line 261 at EigenLayerContest/script/M1_Deploy.s.sol:

        eigenPodBeacon.transferOwnership(communityMultisig);

---

### Mitigation

Use a 2 structure transferOwnership which is safer. safeTransferOwnership, use it is more secure due to 2-stage ownership transfer. https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol
