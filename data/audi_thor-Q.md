Minimal Risk Audit Report
Contract Name: MerkleDelegationTerms
1.Use of view function
The calculateLeafHash function does not modify the state of the contract and only reads from it. It's recommended to declare such functions as view.
before:
function calculateLeafHash(address staker, TokenAndAmount[] calldata tokensAndAmounts) public pure returns (bytes32) {
    // ...
}
after:
function calculateLeafHash(address staker, TokenAndAmount[] calldata tokensAndAmounts) public view returns (bytes32) {
    // ...
}

