## Non Critical Issues

|       | Issue                                                                                               |
| ----- | :-------------------------------------------------------------------------------------------------- |
| NC-1  | ADD A TIMELOCK TO CRITICAL FUNCTIONS                                                                |
| NC-2  | BE EXPLICIT DECLARING TYPES                                                                         |
| NC-3  | USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)                                                |
| NC-4  | GENERATE PERFECT CODE HEADERS EVERY TIME                                                            |
| NC-5  | USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS                                                     |
| NC-6  | FUNCTIONS, PARAMETERS AND VARIABLES IN SNAKE CASE                                                   |
| NC-7  | IMPLEMENTATION CONTRACT MAY NOT BE INITIALIZED                                                      |
| NC-8  | FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES                                             |
| NC-9  | MARK VISIBILITY OF INITIALIZE(…) FUNCTIONS AS EXTERNAL                                              |
| NC-10 | LACK OF EVENT EMISSION AFTER CRITICAL INITIALIZE() FUNCTIONS                                        |
| NC-11 | FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION                                       |
| NC-12 | MISSING EVENT FOR CRITICAL PARAMETER CHANGE                                                         |
| NC-13 | INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS                                                       |
| NC-14 | NO SAME VALUE INPUT CONTROL                                                                         |
| NC-15 | OMISSIONS IN EVENTS                                                                                 |
| NC-16 | BETTER TO HAVE CONFIG FACET, IN CASE SOME UPDATE IS NEEDED IN THE CONFIG.SOL                        |
| NC-17 | FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS                             |
| NC-18 | USE OZ MERKLETREE IMPLEMENTATION INSTEAD OF CREATING A NEW ONE                                      |
| NC-19 | Functions not used internally could be marked external                                              |
| NC-20 | IMPLEMENT SOME TYPE OF VERSION COUNTER THAT WILL BE INCREMENTED AUTOMATICALLY FOR CONTRACT UPGRADES |

### [NC-1] ADD A TIMELOCK TO CRITICAL FUNCTIONS

#### Description:

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

582:     function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {

587:     function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {

839:     function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {

846:     function _setStrategyWhitelister(address newStrategyWhitelister) internal {

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

39:     function setThreshold(uint256 _threshold) external;

```

```solidity
File: src/contracts/interfaces/IDelayedWithdrawalRouter.sol

37:     function setWithdrawalDelayBlocks(uint256 newValue) external;

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

32:     function setPauser(address newPauser) external onlyUnpauser {

37:     function setUnpauser(address newUnpauser) external onlyUnpauser {

41:     function _setPauser(address newPauser) internal {

47:     function _setUnpauser(address newUnpauser) internal {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

100:     function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {

166:     function _setWithdrawalDelayBlocks(uint256 newValue) internal {

```

### [NC-2] BE EXPLICIT DECLARING TYPES

#### Description:

Instead of uint use uint256

#### **Proof Of Concept**

```solidity
File: src/contracts/libraries/Merkle.sol

137:         for (uint i = 0; i < numNodesInLayer; i++) {

145:             for (uint i = 0; i < numNodesInLayer; i++) {

```

### [NC-3] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)

#### Description:

Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, `bytes.concat()` is enabled.

Since version 0.8.4 for appending bytes, `bytes.concat()` can be used instead of `abi.encodePacked(,)`.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

277:             digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));

279:             digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));

```

```solidity
File: src/contracts/libraries/Merkle.sol

138:             layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));

146:                 layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));

```

```solidity
File: src/contracts/pods/EigenPod.sol

461:         return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));

```

### [NC-4] GENERATE PERFECT CODE HEADERS EVERY TIME

#### Description:

I recommend using header for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [NC-5] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS

#### Description:

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes.

Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

17:     bytes32 public constant DOMAIN_TYPEHASH =

20:     bytes32 public constant DEPOSIT_TYPEHASH =

46:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;

70:     IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

24:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;

```

### [NC-6] FUNCTIONS, PARAMETERS AND VARIABLES IN SNAKE CASE

#### Description:

Use camel case for all functions, parameters and variables and snake case for constants.

#### **Proof Of Concept**

```solidity
File: src/contracts/interfaces/IETHPOSDeposit.sol

29:         bytes calldata withdrawal_credentials,

31:         bytes32 deposit_data_root

36:     function get_deposit_root() external view returns (bytes32);

40:     function get_deposit_count() external view returns (bytes memory);

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

47:     function REQUIRED_BALANCE_GWEI() external view returns(uint64);

50:     function REQUIRED_BALANCE_WEI() external view returns(uint256);

```

### [NC-7] IMPLEMENTATION CONTRACT MAY NOT BE INITIALIZED

#### Description:

OpenZeppelin recommends that the initializer modifier be applied to constructors.

Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.

https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

[code4arena example](https://code4rena.com/reports/2022-10-holograph/#16-implementation-contract-may-not-be-initialized

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

6: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

27:     Initializable,

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

4: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

11: contract DelayedWithdrawalRouter is Initializable, OwnableUpgradeable, ReentrancyGuardUpgradeable, Pausable, IDelayedWithdrawalRouter {

```

```solidity
File: src/contracts/pods/EigenPod.sol

4: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

34: contract EigenPod is IEigenPod, Initializable, ReentrancyGuardUpgradeable, EigenPodPausingConstants {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

8: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

31: contract EigenPodManager is Initializable, OwnableUpgradeable, Pausable, IEigenPodManager, EigenPodPausingConstants {

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

8: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

19: contract StrategyBase is Initializable, Pausable, IStrategy {

```

### [NC-8] FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES

#### Description:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.

This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

4: import "@openzeppelin/contracts/interfaces/IERC1271.sol";

5: import "@openzeppelin/contracts/utils/Address.sol";

6: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

7: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

8: import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";

9: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

10: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

11: import "../interfaces/IEigenPodManager.sol";

12: import "../permissions/Pausable.sol";

13: import "./StrategyManagerStorage.sol";

```

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

4: import "../interfaces/IStrategyManager.sol";

5: import "../interfaces/IStrategy.sol";

6: import "../interfaces/IEigenPodManager.sol";

7: import "../interfaces/IDelegationManager.sol";

8: import "../interfaces/ISlasher.sol";

```

```solidity
File: src/contracts/interfaces/IDelegationManager.sol

4: import "./IDelegationTerms.sol";

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

4: import "../libraries/BeaconChainProofs.sol";

5: import "./IEigenPodManager.sol";

6: import "./IBeaconChainOracle.sol";

```

```solidity
File: src/contracts/interfaces/IEigenPodManager.sol

4: import "./IStrategyManager.sol";

5: import "./IEigenPod.sol";

6: import "./IBeaconChainOracle.sol";

7: import "./IPausable.sol";

```

```solidity
File: src/contracts/interfaces/IPausable.sol

4: import "../interfaces/IPauserRegistry.sol";

```

```solidity
File: src/contracts/interfaces/IServiceManager.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import "./IDelegationManager.sol";

```

```solidity
File: src/contracts/interfaces/IStrategy.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

```solidity
File: src/contracts/interfaces/IStrategyManager.sol

4: import "./IStrategy.sol";

5: import "./ISlasher.sol";

6: import "./IDelegationManager.sol";

```

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

5: import "./Merkle.sol";

6: import "../libraries/Endian.sol";

```

```solidity
File: src/contracts/permissions/Pausable.sol

5: import "../interfaces/IPausable.sol";

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

4: import "../interfaces/IPauserRegistry.sol";

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

4: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

6: import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";

7: import "../interfaces/IEigenPodManager.sol";

8: import "../interfaces/IDelayedWithdrawalRouter.sol";

9: import "../permissions/Pausable.sol";

```

```solidity
File: src/contracts/pods/EigenPod.sol

4: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

6: import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";

7: import "@openzeppelin-upgrades/contracts/utils/AddressUpgradeable.sol";

9: import "../libraries/BeaconChainProofs.sol";

10: import "../libraries/BytesLib.sol";

11: import "../libraries/Endian.sol";

13: import "../interfaces/IETHPOSDeposit.sol";

14: import "../interfaces/IEigenPodManager.sol";

15: import "../interfaces/IEigenPod.sol";

16: import "../interfaces/IDelayedWithdrawalRouter.sol";

17: import "../interfaces/IPausable.sol";

19: import "./EigenPodPausingConstants.sol";

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

4: import "@openzeppelin/contracts/utils/Create2.sol";

5: import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";

6: import "@openzeppelin/contracts/proxy/beacon/IBeacon.sol";

8: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

9: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

11: import "../interfaces/IStrategyManager.sol";

12: import "../interfaces/IDelegationManager.sol";

13: import "../interfaces/IEigenPodManager.sol";

14: import "../interfaces/IETHPOSDeposit.sol";

15: import "../interfaces/IEigenPod.sol";

17: import "../interfaces/IBeaconChainOracle.sol";

19: import "../permissions/Pausable.sol";

20: import "./EigenPodPausingConstants.sol";

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

4: import "../interfaces/IStrategyManager.sol";

5: import "../permissions/Pausable.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

8: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";

```

#### Recommended Mitigation Steps:

`import {contract1 , contract2} from "filename.sol";` OR Use specific imports syntax per solidity docs recommendation.

### [NC-9] MARK VISIBILITY OF INITIALIZE(…) FUNCTIONS AS EXTERNAL

#### Description:

If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}\_init function, not the parent public initialize(...) function.

External instead of public would give more sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally).

From a security point of view, it might be safer so that it cannot be called internally by accident in the child contract.

#### **Proof Of Concept**

```solidity
File: src/contracts/strategies/StrategyBase.sol

51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

```

### [NC-10] LACK OF EVENT EMISSION AFTER CRITICAL INITIALIZE() FUNCTIONS

#### Description:

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the initialize() functions.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

146:     function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

59:     function initialize(address owner) external;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

```

```solidity
File: src/contracts/pods/EigenPod.sol

152:     function initialize(address _podOwner) external initializer {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

84:     function initialize(

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

```

### [NC-11] FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION

#### Description:

https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

Use solidity pragma version min. 0.8.13

```solidity
File: src/contracts/core/StrategyManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IDelayedWithdrawalRouter.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IDelegationManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IETHPOSDeposit.sol

12: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IEigenPodManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IPausable.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IPauserRegistry.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IServiceManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/ISlasher.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IStrategy.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IStrategyManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

3: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/libraries/Endian.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/libraries/Merkle.sol

4: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/permissions/Pausable.sol

3: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/EigenPod.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/EigenPodPausingConstants.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

2: pragma solidity =0.8.12;

```

### [NC-12] MISSING EVENT FOR CRITICAL PARAMETER CHANGE

#### Description:

Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

582:     function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {

587:     function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

39:     function setThreshold(uint256 _threshold) external;

```

```solidity
File: src/contracts/interfaces/IDelayedWithdrawalRouter.sol

37:     function setWithdrawalDelayBlocks(uint256 newValue) external;

```

```solidity
File: src/contracts/interfaces/IETHPOSDeposit.sol

19:     event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

32:     function setPauser(address newPauser) external onlyUnpauser {

37:     function setUnpauser(address newUnpauser) external onlyUnpauser {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

100:     function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {

```

### [NC-13] INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

#### Description:

If Return parameters are declared, you must prefix them with ”/// @return”.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the “@return” tag, they do incomplete analysis.

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

Include return parameters in NatSpec comments

```solidity
File: src/contracts/core/StrategyManager.sol

428:         return withdrawalRoot;

671:         return shares;

704:             return true;

707:         return false;

831:                 return;

867:         return (stakerStrategyList[depositor], shares);

872:         return stakerStrategyList[staker].length;

877:         return (

```

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

137:         return Merkle.merkleizeSha256(paddedHeaderFields);

147:         return Merkle.merkleizeSha256(paddedBeaconStateFields);

157:         return Merkle.merkleizeSha256(paddedValidatorFields);

167:         return Merkle.merkleizeSha256(paddedEth1DataFields);

182:         return validatorBalance;

```

```solidity
File: src/contracts/libraries/Endian.sol

10:         return

```

```solidity
File: src/contracts/libraries/Merkle.sol

35:         return processInclusionProofKeccak(proof, leaf, index) == root;

69:         return computedHash;

86:         return processInclusionProofSha256(proof, leaf, index) == root;

120:         return computedHash[0];

152:         return layer[0];

```

```solidity
File: src/contracts/permissions/Pausable.sol

101:         return _paused;

107:         return ((_paused & mask) == mask);

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

106:         return _userWithdrawals[user];

118:         return claimableDelayedWithdrawals;

123:         return _userWithdrawals[user].delayedWithdrawals[index];

128:         return _userWithdrawals[user].delayedWithdrawals.length;

133:         return ((index >= _userWithdrawals[user].delayedWithdrawalsCompleted) && (block.number >= _userWithdrawals[user].delayedWithdrawals[index].blockCreated + withdrawalDelayBlocks));

```

```solidity
File: src/contracts/pods/EigenPod.sol

461:         return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

183:         return pod;

206:         return pod;

211:         return address(ownerToPod[podOwner]) != address(0);

218:         return stateRoot;

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

111:         return newShares;

163:         return "Base Strategy implementation to inherit from for more complex implementations";

174:             return amountShares;

176:             return (_tokenBalance() * amountShares) / totalShares;

187:         return sharesToUnderlyingView(amountShares);

199:             return amountUnderlying;

201:             return (amountUnderlying * totalShares) / tokenBalance;

212:         return underlyingToSharesView(amountUnderlying);

220:         return sharesToUnderlyingView(shares(user));

228:         return sharesToUnderlying(shares(user));

236:         return strategyManager.stakerStrategyShares(user, IStrategy(address(this)));

242:         return underlyingToken.balanceOf(address(this));

```

### [NC-14] NO SAME VALUE INPUT CONTROL

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

73:         delegation = _delegation;

74:         eigenPodManager = _eigenPodManager;

75:         slasher = _slasher;

```

```solidity
File: src/contracts/permissions/Pausable.sol

62:         pauserRegistry = _pauserRegistry;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

46:         eigenPodManager = _eigenPodManager;

```

```solidity
File: src/contracts/pods/EigenPod.sol

142:         ethPOS = _ethPOS;

143:         delayedWithdrawalRouter = _delayedWithdrawalRouter;

144:         eigenPodManager = _eigenPodManager;

145:         REQUIRED_BALANCE_WEI = _REQUIRED_BALANCE_WEI;

154:         podOwner = _podOwner;

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

77:         ethPOS = _ethPOS;

78:         eigenPodBeacon = _eigenPodBeacon;

79:         strategyManager = _strategyManager;

80:         slasher = _slasher;

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

47:         strategyManager = _strategyManager;

57:         underlyingToken = _underlyingToken;

197:         uint256 tokenBalance = _tokenBalance();

```

#### Recommended Mitigation Steps:

Add code like this; `if (oracle == _oracle revert ADDRESS_SAME();`

### [NC-15] OMISSIONS IN EVENTS

#### Description:

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

The events should include the new value and old value where possible.

#### **Proof Of Concept**

```solidity
File: src/contracts/pods/EigenPod.sol

162:         emit EigenPodStaked(pubkey);

222:         emit ValidatorRestaked(validatorIndex);

290:         emit ValidatorOvercommitted(validatorIndex);

```

### [NC-16] BETTER TO HAVE CONFIG FACET, IN CASE SOME UPDATE IS NEEDED IN THE CONFIG.SOL

#### Description:

Better to have config facet, in case some update is needed in the `config.sol`. Therefore, it is not necessary to redeploy the facets that imported config.

### [NC-17] FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

#### Description:

Solidity’s standard naming convention for internal and private functions and variables (apart from constants): the mixedCase format starting with an underscore (\_mixedCase starting with an underscore)

Solidity’s standard naming convention for internal and private constants variables: the snake_case format starting with an underscore (\_mixedCase starting with an underscore) and use ALL_CAPS for naming them.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

35:     uint256 internal constant GWEI_TO_WEI = 1e9;

38:     uint8 internal constant PAUSED_DEPOSITS = 0;

40:     uint8 internal constant PAUSED_WITHDRAWALS = 1;

45:     bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;

```

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

28:     uint8 internal constant MAX_STAKER_STRATEGY_LIST_LENGTH = 32;

```

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

14:     uint256 internal constant NUM_BEACON_BLOCK_HEADER_FIELDS = 5;

15:     uint256 internal constant BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT = 3;

17:     uint256 internal constant NUM_BEACON_BLOCK_BODY_FIELDS = 11;

18:     uint256 internal constant BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT = 4;

20:     uint256 internal constant NUM_BEACON_STATE_FIELDS = 21;

21:     uint256 internal constant BEACON_STATE_FIELD_TREE_HEIGHT = 5;

23:     uint256 internal constant NUM_ETH1_DATA_FIELDS = 3;

24:     uint256 internal constant ETH1_DATA_FIELD_TREE_HEIGHT = 2;

26:     uint256 internal constant NUM_VALIDATOR_FIELDS = 8;

27:     uint256 internal constant VALIDATOR_FIELD_TREE_HEIGHT = 3;

29:     uint256 internal constant NUM_EXECUTION_PAYLOAD_HEADER_FIELDS = 15;

30:     uint256 internal constant EXECUTION_PAYLOAD_HEADER_FIELD_TREE_HEIGHT = 4;

33:     uint256 internal constant NUM_EXECUTION_PAYLOAD_FIELDS = 15;

34:     uint256 internal constant EXECUTION_PAYLOAD_FIELD_TREE_HEIGHT = 4;

38:     uint256 internal constant HISTORICAL_ROOTS_TREE_HEIGHT = 24;

41:     uint256 internal constant HISTORICAL_BATCH_TREE_HEIGHT = 1;

44:     uint256 internal constant STATE_ROOTS_TREE_HEIGHT = 13;

45:     uint256 internal constant BLOCK_ROOTS_TREE_HEIGHT = 13;

48:     uint256 internal constant NUM_WITHDRAWAL_FIELDS = 4;

50:     uint256 internal constant WITHDRAWAL_FIELD_TREE_HEIGHT = 2;

52:     uint256 internal constant VALIDATOR_TREE_HEIGHT = 40;

54:     uint256 internal constant BALANCE_TREE_HEIGHT = 38;

57:     uint256 internal constant WITHDRAWALS_TREE_HEIGHT = 4;

60:     uint256 internal constant EXECUTION_PAYLOAD_INDEX = 9;

63:     uint256 internal constant STATE_ROOT_INDEX = 3;

64:     uint256 internal constant PROPOSER_INDEX_INDEX = 1;

65:     uint256 internal constant SLOT_INDEX = 0;

66:     uint256 internal constant BODY_ROOT_INDEX = 4;

68:     uint256 internal constant STATE_ROOTS_INDEX = 6;

69:     uint256 internal constant BLOCK_ROOTS_INDEX = 5;

70:     uint256 internal constant HISTORICAL_ROOTS_INDEX = 7;

71:     uint256 internal constant ETH_1_ROOT_INDEX = 8;

72:     uint256 internal constant VALIDATOR_TREE_ROOT_INDEX = 11;

73:     uint256 internal constant BALANCE_INDEX = 12;

74:     uint256 internal constant EXECUTION_PAYLOAD_HEADER_INDEX = 24;

75:     uint256 internal constant HISTORICAL_BATCH_STATE_ROOT_INDEX = 1;

78:     uint256 internal constant VALIDATOR_WITHDRAWAL_CREDENTIALS_INDEX = 1;

79:     uint256 internal constant VALIDATOR_BALANCE_INDEX = 2;

80:     uint256 internal constant VALIDATOR_SLASHED_INDEX = 3;

81:     uint256 internal constant VALIDATOR_WITHDRAWABLE_EPOCH_INDEX = 7;

84:     uint256 internal constant BLOCK_NUMBER_INDEX = 6;

85:     uint256 internal constant WITHDRAWALS_ROOT_INDEX = 14;

88:     uint256 internal constant WITHDRAWALS_INDEX = 14;

91:     uint256 internal constant WITHDRAWAL_VALIDATOR_INDEX_INDEX = 1;

92:     uint256 internal constant WITHDRAWAL_VALIDATOR_AMOUNT_INDEX = 3;

95:     uint256 internal constant HISTORICALBATCH_STATEROOTS_INDEX = 1;

98:     uint256 internal constant SLOTS_PER_EPOCH = 32;

100:     bytes8 internal constant UINT64_MASK = 0xffffffffffffffff;

130:     function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

140:     function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {

150:     function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {

160:     function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {

178:    function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

197:     ) internal view {

226:     ) internal view {

249:     ) internal view {

```

```solidity
File: src/contracts/libraries/Endian.sol

7:     ) internal pure returns (uint64 n) {

```

```solidity
File: src/contracts/libraries/Merkle.sol

34:     ) internal pure returns (bool) {

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

85:     ) internal view returns (bool) {

99:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

131:     ) internal pure returns (bytes32) {

```

```solidity
File: src/contracts/permissions/Pausable.sol

22:     uint256 constant internal UNPAUSE_ALL = 0;

23:     uint256 constant internal PAUSE_ALL = type(uint256).max;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

16:     uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;

```

```solidity
File: src/contracts/pods/EigenPod.sol

38:     uint256 internal constant GWEI_TO_WEI = 1e9;

41:     uint256 internal constant VERIFY_OVERCOMMITTED_WINDOW_BLOCKS = 50400;

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

37:     bytes internal constant beaconProxyBytecode = hex"608060405260405161090e38038061090e83398101604081905261002291610460565b61002e82826000610035565b505061058a565b61003e83610100565b6040516001600160a01b038416907f1cf3b03a6cf19fa2baba4df148e9dcabedea7f8a5c07840e207e5c089be95d3e90600090a260008251118061007f5750805b156100fb576100f9836001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156100c5573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906100e99190610520565b836102a360201b6100291760201c565b505b505050565b610113816102cf60201b6100551760201c565b6101725760405162461bcd60e51b815260206004820152602560248201527f455243313936373a206e657720626561636f6e206973206e6f74206120636f6e6044820152641d1c9858dd60da1b60648201526084015b60405180910390fd5b6101e6816001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156101b3573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906101d79190610520565b6102cf60201b6100551760201c565b61024b5760405162461bcd60e51b815260206004820152603060248201527f455243313936373a20626561636f6e20696d706c656d656e746174696f6e206960448201526f1cc81b9bdd08184818dbdb9d1c9858dd60821b6064820152608401610169565b806102827fa3f0ad74e5423aebfd80d3ef4346578335a9a72aeaee59ff6cb3582b35133d5060001b6102de60201b6100641760201c565b80546001600160a01b0319166001600160a01b039290921691909117905550565b60606102c883836040518060600160405280602781526020016108e7602791396102e1565b9392505050565b6001600160a01b03163b151590565b90565b6060600080856001600160a01b0316856040516102fe919061053b565b600060405180830381855af49150503d8060008114610339576040519150601f19603f3d011682016040523d82523d6000602084013e61033e565b606091505b5090925090506103508683838761035a565b9695505050505050565b606083156103c65782516103bf576001600160a01b0385163b6103bf5760405162461bcd60e51b815260206004820152601d60248201527f416464726573733a2063616c6c20746f206e6f6e2d636f6e74726163740000006044820152606401610169565b50816103d0565b6103d083836103d8565b949350505050565b8151156103e85781518083602001fd5b8060405162461bcd60e51b81526004016101699190610557565b80516001600160a01b038116811461041957600080fd5b919050565b634e487b7160e01b600052604160045260246000fd5b60005b8381101561044f578181015183820152602001610437565b838111156100f95750506000910152565b6000806040838503121561047357600080fd5b61047c83610402565b60208401519092506001600160401b038082111561049957600080fd5b818501915085601f8301126104ad57600080fd5b8151818111156104bf576104bf61041e565b604051601f8201601f19908116603f011681019083821181831017156104e7576104e761041e565b8160405282815288602084870101111561050057600080fd5b610511836020830160208801610434565b80955050505050509250929050565b60006020828403121561053257600080fd5b6102c882610402565b6000825161054d818460208701610434565b9190910192915050565b6020815260008251806020840152610576816040850160208701610434565b601f01601f19169190910160400192915050565b61034e806105996000396000f3fe60806040523661001357610011610017565b005b6100115b610027610022610067565b610100565b565b606061004e83836040518060600160405280602781526020016102f260279139610124565b9392505050565b6001600160a01b03163b151590565b90565b600061009a7fa3f0ad74e5423aebfd80d3ef4346578335a9a72aeaee59ff6cb3582b35133d50546001600160a01b031690565b6001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156100d7573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906100fb9190610249565b905090565b3660008037600080366000845af43d6000803e80801561011f573d6000f35b3d6000fd5b6060600080856001600160a01b03168560405161014191906102a2565b600060405180830381855af49150503d806000811461017c576040519150601f19603f3d011682016040523d82523d6000602084013e610181565b606091505b50915091506101928683838761019c565b9695505050505050565b6060831561020d578251610206576001600160a01b0385163b6102065760405162461bcd60e51b815260206004820152601d60248201527f416464726573733a2063616c6c20746f206e6f6e2d636f6e747261637400000060448201526064015b60405180910390fd5b5081610217565b610217838361021f565b949350505050565b81511561022f5781518083602001fd5b8060405162461bcd60e51b81526004016101fd91906102be565b60006020828403121561025b57600080fd5b81516001600160a01b038116811461004e57600080fd5b60005b8381101561028d578181015183820152602001610275565b8381111561029c576000848401525b50505050565b600082516102b4818460208701610272565b9190910192915050565b60208152600082518060208401526102dd816040850160208701610272565b601f01601f1916919091016040019291505056fe416464726573733a206c6f772d6c6576656c2064656c65676174652063616c6c206661696c6564a2646970667358221220d51e81d3bc5ed20a26aeb05dce7e825c503b2061aa78628027300c8d65b9d89a64736f6c634300080c0033416464726573733a206c6f772d6c6576656c2064656c65676174652063616c6c206661696c6564";

```

```solidity
File: src/contracts/pods/EigenPodPausingConstants.sol

10:     uint8 internal constant PAUSED_NEW_EIGENPODS = 0;

12:     uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;

15:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;

17:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;

19:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

22:     uint8 internal constant PAUSED_DEPOSITS = 0;

23:     uint8 internal constant PAUSED_WITHDRAWALS = 1;

28:     uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;

```

### [NC-18] USE OZ MERKLETREE IMPLEMENTATION INSTEAD OF CREATING A NEW ONE

#### Description:

Instead of your own merkle tree lib:

- src/contracts/libraries/BeaconChainProofs.sol
- src/contracts/libraries/Merkle.sol

Use openzeppelin implementation;
https://docs.openzeppelin.com/contracts/4.x/api/utils#MerkleProof

#### **Proof Of Concept**

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

5: import "./Merkle.sol";

```

```solidity
File: src/contracts/libraries/Merkle.sol

20: library Merkle {

```

### [NC-19] Functions not used internally could be marked external

#### **Proof Of Concept**

```solidity
File: src/contracts/pods/EigenPodManager.sol

193:     function getPod(address podOwner) public view returns (IEigenPod) {

```

### [NC-20] IMPLEMENT SOME TYPE OF VERSION COUNTER THAT WILL BE INCREMENTED AUTOMATICALLY FOR CONTRACT UPGRADES

#### Description:

I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

7: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

28:     OwnableUpgradeable,

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

11: contract DelayedWithdrawalRouter is Initializable, OwnableUpgradeable, ReentrancyGuardUpgradeable, Pausable, IDelayedWithdrawalRouter {

```

```solidity
File: src/contracts/pods/EigenPod.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

9: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

31: contract EigenPodManager is Initializable, OwnableUpgradeable, Pausable, IEigenPodManager, EigenPodPausingConstants {

```

## Low Issues

|      | Issue                                                                          |
| ---- | :----------------------------------------------------------------------------- |
| L-1  | USE OWNABLE2STEPUPGRADEABLE INSTEAD OF OWNABLEUPGRADEABLE CONTRACT             |
| L-2  | Arbitrary Jump with Function Type Variable                                     |
| L-3  | Initializing state-variables in proxy-based upgradeable contracts              |
| L-4  | CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE                                 |
| L-5  | The critical parameters in initialize(...) are not set safely                  |
| L-6  | Initializers could be front-run                                                |
| L-7  | INITIALIZE() FUNCTION CAN BE CALLED BY ANYBODY                                 |
| L-8  | LOSS OF PRECISION DUE TO ROUNDING                                              |
| L-9  | MISSING CALLS TO \_\_REENTRANCYGUARD_INIT FUNCTIONS OF INHERITED CONTRACTS     |
| L-10 | UNIFY RETURN CRITERIA                                                          |
| L-11 | OWNER CAN RENOUNCE OWNERSHIP                                                   |
| L-12 | THE SAFETRANSFER FUNCTION DOES NOT CHECK FOR POTENTIALLY SELF-DESTROYED TOKENS |
| L-13 | BLOCK VALUES AS A PROXY FOR TIME                                               |
| L-14 | Account existence check for low-level calls                                    |
| L-15 | USE `SAFETRANSFEROWNERSHIP` INSTEAD OF `TRANSFEROWNERSHIP` FUNCTION            |

### [L-1] MINE - USE OWNABLE2STEPUPGRADEABLE INSTEAD OF OWNABLEUPGRADEABLE CONTRACT

#### Description:

transferOwnership function is used to change Ownership from OwnableUpgradeable.sol.

There is another Openzeppelin Ownable contract (Ownable2StepUpgradeable.sol) has transferOwnership function , use it is more secure due to 2-stage ownership transfer.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

7: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

28:     OwnableUpgradeable,

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

11: contract DelayedWithdrawalRouter is Initializable, OwnableUpgradeable, ReentrancyGuardUpgradeable, Pausable, IDelayedWithdrawalRouter {

```

```solidity
File: src/contracts/pods/EigenPod.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

9: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

31: contract EigenPodManager is Initializable, OwnableUpgradeable, Pausable, IEigenPodManager, EigenPodPausingConstants {

```

#### Recommended Mitigation Steps:

We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.`

### [L-2] Arbitrary Jump with Function Type Variable

#### Description:

Function types are supported in Solidity. This means that a variable of type function can be assigned to a function with a matching signature. The function can then be called from the variable just like any other function. Users should not be able to change the function variable, but in some cases this is possible.

If the smart contract uses certain assembly instructions, mstore for example, an attacker may be able to point the function variable to any other function. This may give the attacker the ability to break the functionality of the contract, and perhaps even drain the contract funds.

Since inline assembly is a way to access the EVM at a low level, it bypasses many important safety features. So it's important to only use assembly if it is necessary and properly understood.

[Source](https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/arbitrary-jump-function-type.md)

[SWC Registry](https://swcregistry.io/docs/SWC-127)

#### **Proof Of Concept**

```solidity
File: src/contracts/libraries/Merkle.sol

53:             assembly {
                    mstore(0x00, computedHash)
                    mstore(0x20, mload(add(proof, i)))
                    computedHash := keccak256(0x00, 0x40)
                    index := div(index, 2)
                }

61:            assembly {
                    mstore(0x00, mload(add(proof, i)))
                    mstore(0x20, computedHash)
                    computedHash := keccak256(0x00, 0x40)
                    index := div(index, 2)
                }

104:            assembly {
                    mstore(0x00, mload(computedHash))
                    mstore(0x20, mload(add(proof, i)))
                    if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
                    index := div(index, 2)
                }

112:           assembly {
                    mstore(0x00, mload(add(proof, i)))
                    mstore(0x20, mload(computedHash))
                    if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
                    index := div(index, 2)
                }

```

### [L-3] Initializing state-variables in proxy-based upgradeable contracts

#### Description:

This should be done in initializer functions and not as part of the state variable declarations in which case they won’t be set.

https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#avoid-initial-values-in-field-declarations

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

146:     function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

59:     function initialize(address owner) external;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

```

```solidity
File: src/contracts/pods/EigenPod.sol

152:     function initialize(address _podOwner) external initializer {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

84:     function initialize(

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

```

### [L-4] CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE

#### Description:

The critical procedures should be two step process.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

582:     function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {

587:     function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {

839:     function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {

846:     function _setStrategyWhitelister(address newStrategyWhitelister) internal {

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

39:     function setThreshold(uint256 _threshold) external;

```

```solidity
File: src/contracts/interfaces/IDelayedWithdrawalRouter.sol

37:     function setWithdrawalDelayBlocks(uint256 newValue) external;

```

```solidity
File: src/contracts/interfaces/ISlasher.sol

44:     function resetFrozenStatus(address[] calldata frozenAddresses) external;

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

32:     function setPauser(address newPauser) external onlyUnpauser {

37:     function setUnpauser(address newUnpauser) external onlyUnpauser {

41:     function _setPauser(address newPauser) internal {

47:     function _setUnpauser(address newUnpauser) internal {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

100:     function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {

166:     function _setWithdrawalDelayBlocks(uint256 newValue) internal {

```

#### Recommended Mitigation Steps:

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

### [L-5] The critical parameters in initialize(...) are not set safely

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

146:     function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

59:     function initialize(address owner) external;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

```

```solidity
File: src/contracts/pods/EigenPod.sol

152:     function initialize(address _podOwner) external initializer {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

84:     function initialize(

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

```

### [L-6] Initializers could be front-run

#### Description:

Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

146:     function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)

148:         initializer

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

59:     function initialize(address owner) external;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

```

```solidity
File: src/contracts/pods/EigenPod.sol

152:     function initialize(address _podOwner) external initializer {

152:     function initialize(address _podOwner) external initializer {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

84:     function initialize(

89:     ) external initializer {

179:         pod.initialize(msg.sender);

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

```

### [L-7] INITIALIZE() FUNCTION CAN BE CALLED BY ANYBODY

#### Description:

`initialize()` function can be called anybody when the contract is not initialized.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

146:  function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer
    {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

```

```solidity
File: src/contracts/pods/EigenPod.sol

152:     function initialize(address _podOwner) external initializer {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

84: function initialize(
        IBeaconChainOracle _beaconChainOracle,
        address initialOwner,
        IPauserRegistry _pauserRegistry,
        uint256 _initPausedStatus
    ) external initializer {

```

### [L-8] LOSS OF PRECISION DUE TO ROUNDING

#### **Proof Of Concept**

```solidity
File: src/contracts/strategies/StrategyBase.sol

99:                 newShares = (amount * totalShares) / priorTokenBalance;

152:             amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;

176:             return (_tokenBalance() * amountShares) / totalShares;

201:             return (amountUnderlying * totalShares) / tokenBalance;

```

### [L-9] MISSING CALLS TO \_\_REENTRANCYGUARD_INIT FUNCTIONS OF INHERITED CONTRACTS

#### Description:

Most contracts use the delegateCall proxy pattern and hence their implementations require the use of `initialize()` functions instead of constructors. This requires derived contracts to call the corresponding init functions of their inherited base contracts. This is done in most places except a few.

Impact: The inherited base classes do not get initialized which may lead to undefined behavior.

Missing call to `__ReentrancyGuard_init`

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

146:     function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

59:     function initialize(address owner) external;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

```

```solidity
File: src/contracts/pods/EigenPod.sol

152:     function initialize(address _podOwner) external initializer {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

84:     function initialize(

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

```

#### Recommended Mitigation Steps:

Add missing calls to init functions of inherited contracts.

### [L-10] UNIFY RETURN CRITERIA

#### Description:

In contracts, sometimes the name of the return variable is not defined and sometimes is, unifying the way of writing the code makes the code more uniform and readable.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

225:         returns (uint256 shares)

260:         returns (uint256 shares)

340:         returns (bytes32)

658:         returns (uint256 shares)

681:         returns (bool)

857:     function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory) {

871:     function stakerStrategyListLength(address staker) external view returns (uint256) {

876:     function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {

```

### [L-11] OWNER CAN RENOUNCE OWNERSHIP

#### Description:

Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The Openzeppelin’s Ownable used in this project contract implements `renounceOwnership`. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

7: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

28:     OwnableUpgradeable,

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

11: contract DelayedWithdrawalRouter is Initializable, OwnableUpgradeable, ReentrancyGuardUpgradeable, Pausable, IDelayedWithdrawalRouter {

```

```solidity
File: src/contracts/pods/EigenPod.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

9: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

31: contract EigenPodManager is Initializable, OwnableUpgradeable, Pausable, IEigenPodManager, EigenPodPausingConstants {

```

#### Recommended Mitigation Steps:

We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.`

### [L-12] THE SAFETRANSFER FUNCTION DOES NOT CHECK FOR POTENTIALLY SELF-DESTROYED TOKENS

#### Description:

If a pair gets created and after a while one of the tokens gets self-destroyed (maybe because of a bug) then `safeTransfer` would still succeed. It’s probably a good idea to check if the contract still exists by checking the bytecode length.

#### **Proof Of Concept**

```solidity
File: src/contracts/strategies/StrategyBase.sol

155:         underlyingToken.safeTransfer(depositor, amountToSend);

```

### [L-13] BLOCK VALUES AS A PROXY FOR TIME

#### Description:

Contracts often need access to time values to perform certain types of functionality. Values such as block.timestamp, and block.number can give you a sense of the current time or a time delta, however, they are not safe to use for most purposes.

In the case of block.timestamp, developers often attempt to use it to trigger time-dependent events. As Ethereum is decentralized, nodes can synchronize time only to some degree. Moreover, malicious miners can alter the timestamp of their blocks, especially if they can gain advantages by doing so. However, miners cant set a timestamp smaller than the previous one (otherwise the block will be rejected), nor can they set the timestamp too far ahead in the future. Taking all of the above into consideration, developers cant rely on the preciseness of the provided timestamp.

As for block.number, considering the block time on Ethereum is generally about 14 seconds, it`s possible to predict the time delta between blocks. However, block times are not constant and are subject to change for a variety of reasons, e.g. fork reorganisations and the difficulty bomb. Due to variable block times, block.number should also not be relied on for precise calculations of time.

Reference: https://swcregistry.io/docs/SWC-116

Reference: (https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/timestamp-dependence.md)

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

263:             expiry >= block.timestamp,

405:                 withdrawalStartBlock: uint32(block.number),

761:         require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

64:                 blockCreated: uint32(block.number)

133:         return ((index >= _userWithdrawals[user].delayedWithdrawalsCompleted) && (block.number >= _userWithdrawals[user].delayedWithdrawals[index].blockCreated + withdrawalDelayBlocks));

146:             if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {

```

```solidity
File: src/contracts/pods/EigenPod.sol

249:         require(oracleBlockNumber + VERIFY_OVERCOMMITTED_WINDOW_BLOCKS >= block.number,

455:         mostRecentWithdrawalBlockNumber = uint32(block.number);

```

### [L-14] Account existence check for low-level calls

#### Description:

Low-level calls `call`/`delegatecall`/`staticcall` return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.

Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-callsn

#### **Proof Of Concept**

```solidity
File: src/contracts/libraries/Merkle.sol

107:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}

115:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}

```

#### Recommended Mitigation Steps:

In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

### [L-15] USE `SAFETRANSFEROWNERSHIP` INSTEAD OF `TRANSFEROWNERSHIP` FUNCTION

#### Description:

transferOwnership function is used to change Ownership from Owned.sol.

Use a 2 structure transferOwnership which is safer.

safeTransferOwnership, use it is more secure due to 2-stage ownership transfer.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

152:         _transferOwnership(initialOwner);

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

50:         _transferOwnership(initOwner);

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

91:         _transferOwnership(initialOwner);

```

#### Recommended Mitigation Steps:

Use Ownable2Step.sol
