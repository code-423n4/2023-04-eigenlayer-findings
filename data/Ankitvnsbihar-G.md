
## [G-01] A modifier used only once and not being inherited should be inlined to save gas.
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

  > In StrategyManager.sol , in line no. 119

 **modifier onlyStrategiesWhitelistedForDeposit(IStrategy strategy) {
 require(strategyIsWhitelistedForDeposit[strategy], "StrategyManager.onlyStrategiesWhitelistedForDeposit: strategy not whitelisted");
        _;
    }**



The above modifer is only used in thus function(line no 655) i.e  function _depositIntoStrategy.



 



## so after update(the modifier should be removed and its content should be inlined in the function)

     **function _depositIntoStrategy(address depositor, IStrategy strategy, IERC20 token, uint256 amount)
        internal

       -- onlyStrategiesWhitelistedForDeposit(strategy)

        returns (uint256 shares)
    {


         ++ require(strategyIsWhitelistedForDeposit[strategy], "StrategyManager.onlyStrategiesWhitelistedForDeposit: strategy not whitelisted");


        // transfer tokens from the sender to the strategy
        token.safeTransferFrom(msg.sender, address(strategy), amount);

        // deposit the assets into the specified strategy and get the equivalent amount of shares in that strategy
        shares = strategy.deposit(token, amount);

        // add the returned shares to the depositor's existing shares for this strategy
        _addShares(depositor, strategy, shares);

        emit Deposit(depositor, token, strategy, shares);

        return shares;
    }**

