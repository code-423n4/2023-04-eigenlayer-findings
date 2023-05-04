# Low/QA Report
## Low Risk Issues
| # | Findings | Instances |
| --- | --- | --- |
| [L-01] | Missing zero address validation in setter functions | 1 |
| [L-02] | Recipient can reject or unsupport receiving ETH making the transaction revert | 1 |

---

### [L-01] Missing zero address validation in setter functions
#### Permalinks
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L161-L163
```solidity
File: src/contracts/pods/EigenPodManager.sol

161:    function updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) external onlyOwner {
162:        _updateBeaconChainOracle(newBeaconChainOracle);
163:     }

...

186:    function _updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) internal {
187:        beaconChainOracle = newBeaconChainOracle;
188:        emit BeaconOracleUpdated(address(newBeaconChainOracle));
189:    }
```

#### Description
The address can be set to `address(0)` which could result in unexpected behavior or brick the operation. 

#### Recommended Mitigation Steps
Setters of address type parameters should include a zero-address validation.

---

### [L-02] Recipient can reject or unsupport receiving ETH making the transaction revert
#### Permalinks
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L160
```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

159:        if (amountToSend != 0) {
160:            AddressUpgradeable.sendValue(payable(recipient), amountToSend);
161:        }
```

```solidity
File: @openzeppelin-upgrades/contracts/utils/AddressUpgradeable.sol
// Refference: https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/dd8ca8adc47624c5c5e2f4d412f5f421951dcc25/contracts/utils/AddressUpgradeable.sol#L64-L69

64:    function sendValue(address payable recipient, uint256 amount) internal {
65:        require(address(this).balance >= amount, "Address: insufficient balance");
66:
67:        (bool success, ) = recipient.call{value: amount}("");
68:        require(success, "Address: unable to send value, recipient may have reverted");
69:    }
```

#### Description
Staked ETH of the EigenPod owner will be locked with a certain delay when withdraw. After the delay has passed, anyone can call `claimDelayedWithdrawals(podOwnerAddress, limit)` on the pod owner behalf. However, if the pod owner is a contract, it can deny or unsupport receiving ETH. Therefore, the recipient (the pod owner) has the ability to revert the transaction.

#### Recommended Mitigation Steps
Consider sending as Wrapped ETH (WETH) instead, it is commonly used by many protocols.
Note: This should be a very rare case, as pod owners should not deny their own funds.

---

## Non-Critical Issues
| # | Findings | Instances |
| --- | --- | --- |
| [N-01] | `eigenPodManager.getPod(podOwner)` cannot guarantee existence of pods | 1 |

---

### [N-01] `eigenPodManager.getPod(podOwner)` cannot guarantee existence of pods
#### Permalinks
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L39-L42
```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

39:    modifier onlyEigenPod(address podOwner) {
40:        require(address(eigenPodManager.getPod(podOwner)) == msg.sender, "DelayedWithdrawalRouter.onlyEigenPod: not podOwner's EigenPod");
41:        _;
42:    }
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L193-L207
```solidity
File: src/contracts/pods/EigenPodManager.sol

193:    function getPod(address podOwner) public view returns (IEigenPod) {
194:        IEigenPod pod = ownerToPod[podOwner];
195:        // if pod does not exist already, calculate what its address *will be* once it is deployed
196:        if (address(pod) == address(0)) {
197:            pod = IEigenPod(
198:                Create2.computeAddress(
199:                    bytes32(uint256(uint160(podOwner))), //salt
200:                    keccak256(abi.encodePacked(
201:                        beaconProxyBytecode, 
202:                        abi.encode(eigenPodBeacon, "")
203:                    )) //bytecode
204:                ));
205:        }
206:        return pod;
207:    }
```

#### Description
If the caller does not yet own a pod, `getPod()` returns a pre-deterministic pod address rather than an existing one. Someone could possibly deploy a contract to the same address as the pre-deterministic contract. Therefore, the statement `address(eigenPodManager.getPod(podOwner)) == msg.sender` cannot definitively verify that the caller is an EigenPod.

#### Recommended Mitigation Steps
Use the `hasPod()` to verify that the caller already own a pod that was properly created through the Pod Manager contract. Then, verify that the caller is the Eigen Pod.

```diff
File: src/contracts/pods/DelayedWithdrawalRouter.sol

39:    modifier onlyEigenPod(address podOwner) {
+          require(eigenPodManager.hasPod(podOwner), "Not a pod owner")
40:        require(address(eigenPodManager.getPod(podOwner)) == msg.sender, "DelayedWithdrawalRouter.onlyEigenPod: not podOwner's EigenPod");
41:        _;
42:    }
```