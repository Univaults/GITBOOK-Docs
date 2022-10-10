# Asset liquidation

**Liquidations**

The health of the Univaults Protocol is dependent on the health of the collateralised positions within the protocol, also known as the 'health factor'. When the 'health factor' of an account's total loans is below 1, anyone can make a liquidationCall() to the Pool contract, pay back part of the debt owed and receive discounted collateral in return (also known as the liquidation bonus). This incentivises third parties to participate in the health of the overall protocol, by acting in their own interest (to receive the discounted collateral) and as a result, ensure borrows are sufficiently collateralize.

There are multiple ways to participate in liquidations: By calling the liquidationCall() directly in the Pool contract. By creating your own automated bot or system to liquidate loans.



**Executing the liquidation call**

Once you have the account(s) to liquidate, you will need to calculate the amount of collateral that can be liquidated:

Use getUserReserveData() Max debt that be cleared by single liquidation call is given by the DEFAULT\_LIQUIDATION\_CLOSE\_FACTOR(when CLOSE\_FACTOR\_HF\_THRESHOLD < HF < 1) or MAX\_LIQUIDATION\_CLOSE\_FACTOR (when HF < CLOSE\_FACTOR\_HF\_THRESHOLD)

debtToCover = (userStableDebt + userVariableDebt) \* LiquidationCloseFactor

You can pass uint(-1), i.e. MAX\_UINT, as the debtToCover to liquidate the maximum amount allowed.

Max amount of collateral that can be liquidated to cover debt is given by the current liquidationBonus for the reserves that have usageAsCollateralEnabled as true. maxAmountOfCollateralToLiquidate = (debtAssetPrice \* debtToCover \* liquidationBonus)/ collateralPrice



**Calculating profitability vs gas cost**

One way to calculate the profitability is the following:

Store and retrieve each collateral's relevant details such as address, decimals used and liquidation bonus.

Get the user's collateral balance (mTokenBalance).

Get the asset's price according to the Univaults oracle contract using getAssetPrice().

The maximum collateral bonus received on liquidation is given by the maxAmountOfCollateralToLiquidate \* (1 - liquidationBonus) \* collateralAssetPriceEth

The maximum cost of your transaction will be you gas price multiplied by the amount of gas used. You should be able to get a good estimation of the gas amount used by calling estimateGas via your web3 provider.

Your approximate profit will be the value of the collateral bonus minus the cost of your transaction.



**Appendix**

How is health factor calculated?

The health factor is calculated from the user's total collateral, i.e. all reserves for which usageAsCollateral is enabled, balance (in ETH) multiplied by the liquidation threshold percentage for all the user's outstanding assets, divided by the user's total borrow balance across all reserves (in ETH).

This can be calculated both off-chain and on-chain, see Univaults-docs for developers respectively for reference.



**How is liquidation bonus determined?**

Liquidation bonuses for all the assets are evaluated and determined based on each asset's liquidity risk and updated via Univaults Governance process.
