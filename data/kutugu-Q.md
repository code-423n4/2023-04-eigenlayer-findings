# Findings Summary

| ID     | Title                                                                               | Severity |
| ------ | ----------------------------------------------------------------------------------- | -------- |
| [L-01] | Anyone can call verifyWithdrawalCredentialsAndBalance && verifyAndProcessWithdrawal | Low      |

# Detailed Findings

# [L-01] Anyone can call verifyWithdrawalCredentialsAndBalance && verifyAndProcessWithdrawal

## Link

- [verifyWithdrawalCredentialsAndBalance](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#LL175C52-L175C52)
- [verifyAndProcessWithdrawal](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L305)

## Description

This may not be in line with podOwner's expectations, due to factors such as taxes.    

## Recommendations

Only podOwner or authorized users are allowed to operate.   
