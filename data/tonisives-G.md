# On a hard fork, the chain id will be recalculated for each tx

## Detail

`keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));` is quite an expensive operation. If there is a hard fork, then this function will be called for each tx in `deposit` and `delegateBySignature`.

It is not necessary to calculate the domain separator on each tx, instead the `ORIGINAL_CHAIN_ID` can be replaced by the hard forked one. Then the calculation is done only once.

## Impact

User has to pay more gas.

## Code Snippet

DelegationManager

```solidity

function delegateToBySignature
	...
	if (block.chainid != ORIGINAL_CHAIN_ID) {
	    bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
	    digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
	} else{
	    digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
	}
```

[link](https://github.com/code-423n4/2023-04-eigenlayer/blob/b8036eb312ec2235f36aadef552c41efde39cbbc/src/contracts/core/DelegationManager.sol/#L107)

StrategyManager

```solidity
function depositIntoStrategyWithSignature
	...
	//if chainid has changed, we must re-compute the domain separator
  if (block.chainid != ORIGINAL_CHAIN_ID) {
      bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
      digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
  } else {
      digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
  }
```

[link](https://github.com/code-423n4/2023-04-eigenlayer/blob/b8036eb312ec2235f36aadef552c41efde39cbbc/src/contracts/core/StrategyManager.sol/#L275)


## Recommendation

After calculating the new `DOMAIN_SEPARATOR` on a hard fork, set the `ORIGINAL_CHAIN_ID` to the new chain id. Then you don’t have to calculate the domain separator again for each tx.



# In queueWithdrawal, stakerStrategyList[msg.sender].length == 0 check is not necessary

StrategyManager checks `stakerStrategyList[msg.sender].length == 0`

```solidity
/**
 * Checking that `stakerStrategyList[msg.sender].length == 0` is not strictly necessary here, but prevents reverting very late in logic,
 * in the case that 'undelegate' is set to true but the `msg.sender` still has active deposits in EigenLayer.
 */
if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
    _undelegate(msg.sender);
}
```

In the next function call, `_undelegate(msg.sender);`, has this as the first check as well.

```solidity
function _undelegate(address depositor) internal {
  require(stakerStrategyList[depositor].length == 0, "StrategyManager._undelegate: depositor has active deposits");
```

This is unnecessary double read from storage.

[link](https://github.com/code-423n4/2023-04-eigenlayer/blob/b8036eb312ec2235f36aadef552c41efde39cbbc/src/contracts/core/StrategyManager.sol/#L422)


# onlyOwner functions don’t have to have nonReentrant modifier

For example:
```solidity
function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)
    external
    onlyOwner
    onlyFrozen(queuedWithdrawal.delegatedAddress)
    nonReentrant
{
```

Owner is a trusted entity and is not expected to try a reentrancy attack.