## [G-1] state variables can be closely packed
There is 1 instance of it and the permalink is
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L35-L45
here `bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;` which takes 4 bytes of storage can be clubbed with `uint8 internal constant PAUSED_WITHDRAWALS = 1;` to save 1 storage slot ~2100 of gas
can be saved

    uint256 internal constant GWEI_TO_WEI = 1e9;

    // index for flag that pauses deposits when set
    uint8 internal constant PAUSED_DEPOSITS = 0;
    // index for flag that pauses withdrawals when set
    uint8 internal constant PAUSED_WITHDRAWALS = 1;

    uint256 immutable ORIGINAL_CHAIN_ID;

    // bytes4(keccak256("isValidSignature(bytes32,bytes)")
    bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;

to

    uint256 internal constant GWEI_TO_WEI = 1e9;                 // 32 bytes

    // index for flag that pauses deposits when set
    uint8 internal constant PAUSED_DEPOSITS = 0;                // 1 byte
    // index for flag that pauses withdrawals when set
    uint8 internal constant PAUSED_WITHDRAWALS = 1;           // 1 byte
    // bytes4(keccak256("isValidSignature(bytes32,bytes)")   // 4 bytes
    bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;

    uint256 immutable ORIGINAL_CHAIN_ID;                   // 32 bytes

