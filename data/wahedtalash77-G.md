**[G-1]** Don’t initialize variables with default value.

>385 :    for (uint256 i = 0; i < strategies.length;) {
>466 :    for(uint256 i = 0; i < queuedWithdrawals.length; i++) {
>498 :   for (uint256 i = 0; i < strategiesLength;) {
>560 :   for (uint256 i = 0; i < strategiesLength;) {
>594 :  for (uint256 i = 0; i < strategiesToWhitelistLength;) {
>609 :  for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {
>724 :   uint256 j = 0;
>780 :  for (uint256 i = 0; i < strategiesLength;) {
>797 :  for (uint256 i = 0; i < strategiesLength;) {
>861 : for (uint256 i = 0; i < strategiesLength;) {
>           
>https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol


**[G-2]** Use solidity version 0.8.19 to gain some gas boost
>Upgrade to the latest solidity version 0.8.19 to get additional gas savings.
> 
>https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L2
>
>https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L2
> 
>...
>See latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/