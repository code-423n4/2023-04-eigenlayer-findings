## for loops used in StrategyManager.sol might consume huge amount of gas if the array lengths are too long
#remediation
Always try to use mappings when you expect a long list of values.