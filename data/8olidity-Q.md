## unused variable

```solidity
src/contracts/libraries/BeaconChainProofs.sol:
   17:     uint256 internal constant NUM_BEACON_BLOCK_BODY_FIELDS = 11; //@audit 

   28  
   29:     uint256 internal constant NUM_EXECUTION_PAYLOAD_HEADER_FIELDS = 15; //@audit 

   32  
   33:     uint256 internal constant NUM_EXECUTION_PAYLOAD_FIELDS = 15; //@audit 
   34:     uint256 internal constant EXECUTION_PAYLOAD_FIELD_TREE_HEIGHT = 4; //@audit 
   35  


   38:     uint256 internal constant HISTORICAL_ROOTS_TREE_HEIGHT = 24; //@audit 
   39  
 
   41:     uint256 internal constant HISTORICAL_BATCH_TREE_HEIGHT = 1; //@audit 
   42  

   44:     uint256 internal constant STATE_ROOTS_TREE_HEIGHT = 13; //@audit 


   48:     uint256 internal constant NUM_WITHDRAWAL_FIELDS = 4; //@audit 



   63:     uint256 internal constant STATE_ROOT_INDEX = 3; //@audit 
   64:     uint256 internal constant PROPOSER_INDEX_INDEX = 1; //@audit 



   68:     uint256 internal constant STATE_ROOTS_INDEX = 6; //@audit 

   70:     uint256 internal constant HISTORICAL_ROOTS_INDEX = 7; //@audit 
   71:     uint256 internal constant ETH_1_ROOT_INDEX = 8; //@audit 

   74:     uint256 internal constant EXECUTION_PAYLOAD_HEADER_INDEX = 24; //@audit 
   75:     uint256 internal constant HISTORICAL_BATCH_STATE_ROOT_INDEX = 1; //@audit 
   76  


   79:     uint256 internal constant VALIDATOR_BALANCE_INDEX = 2; //@audit 

   85:     uint256 internal constant WITHDRAWALS_ROOT_INDEX = 14; //@audit 
   86  


   95:     uint256 internal constant HISTORICALBATCH_STATEROOTS_INDEX = 1; //@audit 
   96  

   99  
  100:     bytes8 internal constant UINT64_MASK = 0xffffffffffffffff; //@audit 
  101
```


## code redundancy

The `if` statement will never be executed, because the value of `ORIGINAL_CHAIN_ID` has been set to `block.chainid` in the constructor, so it is impossible for `block.chainid != ORIGINAL_CHAIN_ID`

```solidity
File: src/contracts/core/DelegationManager.sol
if (block.chainid != ORIGINAL_CHAIN_ID) {
            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));//@audit 
            digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
        } else{
            digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
        }

```