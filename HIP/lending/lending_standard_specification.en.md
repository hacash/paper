Hacash Mortgage Lending Specification Standard Document (HIP-2, HIP-3, HIP-4)
===

This document describes the mortgage locking method of the hacash blockchain coins, Diamond HACD and transferred Bitcoin (BTC) and the mechanism for issuing loans from these coins to the main chain coin - HAC. We also define the technical specifications such as interest rate parameters for signing and operating these mortgage lending contracts between users.

This document does not discuss the technical implementation details in detail, but roughly describes the implementation method, focusing on the planning of the economic model and the setting of parameters. The goal is to become a Hacash Improvement Proposals (Hacash Improvement Proposals, or HIP) draft that can be read, understood and discussed.

The goal of the proposed mortgage lending in the Hacash main network, is to meet the currency liquidity needs of HAC holders, and to stabilize short-term currency fluctuations through market arbitrage mechanisms.  The intention is that this monetary facility will stimulate a growing HAC-based economy which is self-regulating and provides stability.

HIP-2 Diamond HACD System Mortgage Lending
---

Core objective: Increase the short-term supply elasticity of HAC currency through arbitrage techniques and market-based mechanisms. When market speculators believe that the price of HAC is overvalued, they can lend HAC through a mortgage of diamonds,  and use/sell them in the market. If the HAC price drops, speculators can buy HAC at a lower price and redeem the diamonds which had previously been mortgaged. This mechanism will help stabilize short-term price fluctuations in HAC, as there is a large value of HACD which can be stored or released as the price changes. The HAC lent at the time of mortgage is issued as a miner coinbase issuance, and the HAC issued is effectively an IOU, which is later destroyed at the time of mortgage redemption.

We propose an annual loan interest rate of 5%, the shortest mortgage to be 35 days (10,000 blocks), and the longest mortgage 2 years (200,000 blocks).  This means the maximum interest rate payable would be 10%, periods shorter than 35 days would be rounded up to 35 days. Each 35 day period interest will be charged at 0.5%. At the beginning of the mortgage, the user selects the mortgage period in units of 35 days in the range of 1-20, and this period determines the mortgage interest payable. After the mortgage transaction is committed to the blockchain, the interest set will be charged regardless of when the mortgage is redeemed (Note: 10% interest will be charged if you choose to mortgage for 2 years but redeem within one week)

The user can only redeem at their own HAC address during the mortgage period, the <b> private redemption phase </b>. After the redemption period is exceeded, the mortgage transaction enters the <b>public redemption phase</b>, when the user can choose to redeem to another HAC address. The duration and interest rate of the public redemption period and the private redemption period are equal.

At the end of the public redemption period, the contract enters the <b>auction stage</b>, anyone can bid on the set interest, and the interest is reduced by 0.5% every 35 days (as a Dutch auction), until the bid amount is reduced to 90% of the loan amount.  This sets the arbitrage minimum to 10% of the interest.

From the initial history the average price of each diamond burned is approximately 8 HAC, we set the loanable value of the first 32,000 diamonds to be 8 HAC, and the subsequent loanable value of diamonds to be determined by the number of HAC rounded up by the average burned amount of HAC during the HACD mining process, the lowest loanable amount is 1 HAC, the maximum is unlimited. The loanable amount of each diamond is determined at the time of mining, and will never be changed once determined.

Summary : each HACD, can be used to loan up to approx 8 HAC, for a period of 1 month to 2 years, 1% fee chargeable at the start, 0.5% per 35 days interest payable at redemption in various ways - by the user to his own address during the private redemption period, to another user during the public redemption period, and by a Dutch auction on the interest payable during the public auction period.


HIP-3 Bitcoin BTC system mortgage lending
---

Core objective: Provide an economy of HAC mortgage interest and redemption using transferred BTC as collateral.  Through the mechanism of dynamic interest rate and loan amount determined by algorithm, a mortgage contract can be committed to the HAC blockchain, and the loan amount and interest rate are completely determined by market flexibility, such that the overall mortgage ratio is at a balance point of mortgage lending. The HAC lent at the time of mortgage is issued as a miner coinbase issuance, and the principal and interest of the HAC returned at the time of redemption will be destroyed by the system.

The minimum number of mortgaged bitcoins is 0.01, which is called "one share"; there is no limit on the minimum number of mortgages for a single time.
The system records the "total mortgage ratio" of all transferred BTC, and determines the current mortgage loan amount and interest rate in real time according to the total mortgage ratio through a curve set by an algorithm. When the mortgage ratio is low, the amount of HAC that can be borrowed is large and the interest rate is low. When the total mortgage ratio increases, the number of loans available will be reduced according to the algorithm, and the interest rate will increase until a limit is unacceptable in the market. See the appendix for the number of borrowable lines and interest rates.

Loan interest is paid in advance, not paid at the time of redemption, and needs to be deducted from the users account in advance, that is, the amount of HAC in the account that must be used for no less than the loan interest before the loan occurs.

The mortgage time is fixed at 100,000 blocks for about one year, after which it enters the <b>public redemption period</b>. The redeemable period and interest are equal to that in the private redemption period, both time periods are one year.

After the public redemption period, the contract enters the auction period. The auction amount starts from the private redemption amount, with a period of 1 million blocks about ten years, and linearly decreases to 0. The collateralized transferred BTC will be redeemed based on the current relative prices of HAC and BTC at some point in the next 10 years. This ensures that any mortgaged Bitcoin will be redeemed by a maximum period of 12 years.

Summary: transferred BTC can be loaned to receive HAC at a 1% fee + interest rate governed by an algorithm, with interest payable in advance. The loan will be redeemed within a 12 year period with a sliding scale, and auction period.


HIP-4 users mortgage diamond or bitcoin loan HAC contract
---

Core goal: Users who own diamonds, bitcoins, and have HAC currency can sign a loan contract without entrusted risk on the chain, freely agree on the terms of collateral, loan amount, interest and redemption period, and follow the plan carried out. It can be matched to meet the needs of both the asset side and the liquidity provider.

Both diamonds and Bitcoin can be mortgaged in the same loan contract, or only one of them can be mortgaged. There is no limit to the number of collaterals.

Freely agree on the loan amount of HAC, the final redemption time, the redemption amount (which also determines the interest rate) and so on. After the mortgage expires, the lender has the right to obtain all the collateral immediately.

You can set whether to support redeemable after expiration, whether to open auction after expiration and other mechanisms.

The system will destroy 1% of the loaned amount as a handling fee, which is paid by the lender.

appendix:
---

Bitcoin System Mortgage Borrowing Quantity and Interest Rate Table


|Total Mortgage Ratio|HAC Borrowable Amount|Prepaid Interest|Actual Borrow|Annual Interest Rate|
|---|---|---|---|---|
|0.00% | 400.00 | 8.00 | 392.00 | 2.04%|
|0.50% | 380.00 | 7.60 | 372.40 | 2.04%|
|1.00% | 360.00 | 7.20 | 352.80 | 2.04%|
|1.50% | 340.00 | 6.80 | 333.20 | 2.04%|
|2.00% | 320.00 | 6.40 | 313.60 | 2.04%|
|2.50% | 300.00 | 6.00 | 294.00 | 2.04%|
|3.00% | 280.00 | 5.60 | 274.40 | 2.04%|
|3.50% | 260.00 | 5.20 | 254.80 | 2.04%|
|4.00% | 240.00 | 4.80 | 235.20 | 2.04%|
|4.50% | 220.00 | 4.40 | 215.60 | 2.04%|
|5.00% | 191.00 | 3.82 | 187.18 | 2.04%|
|5.50% | 172.82 | 3.46 | 169.36 | 2.04%|
|6.00% | 157.67 | 3.15 | 154.51 | 2.04%|
|6.50% | 144.85 | 2.90 | 141.95 | 2.04%|
|7.00% | 133.86 | 2.68 | 131.18 | 2.04%|
|7.50% | 124.33 | 2.49 | 121.85 | 2.04%|
|8.00% | 116.00 | 2.32 | 113.68 | 2.04%|
|8.50% | 108.65 | 2.17 | 106.47 | 2.04%|
|9.00% | 102.11 | 2.04 | 100.07 | 2.04%|
|9.50% | 96.26 | 1.93 | 94.34 | 2.04%|
|10.00% | 91.00 | 1.82 | 89.18 | 2.04%|
|10.50% | 86.24 | 1.72 | 84.51 | 2.04%|
|11.00% | 81.91 | 1.64 | 80.27 | 2.04%|
|12.00% | 74.33 | 1.49 | 72.85 | 2.04%|
|13.00% | 67.92 | 1.36 | 66.56 | 2.04%|
|14.00% | 62.43 | 1.25 | 61.18 | 2.04%|
|15.00% | 57.67 | 1.15 | 56.51 | 2.04%|
|16.00% | 53.50 | 1.07 | 52.43 | 2.04%|
|17.00% | 49.82 | 1.00 | 48.82 | 2.05%|
|18.00% | 46.56 | 1.00 | 45.56 | 2.20%|
|19.00% | 43.63 | 1.00 | 42.63 | 2.35%|
|20.00% | 41.00 | 1.00 | 40.00 | 2.50%|
|21.00% | 38.62 | 1.00 | 37.62 | 2.66%|
|22.00% | 36.45 | 1.00 | 35.45 | 2.82%|
|23.00% | 34.48 | 1.00 | 33.48 | 2.99%|
|24.00% | 32.67 | 1.00 | 31.67 | 3.16%|
|25.00% | 31.00 | 1.00 | 30.00 | 3.33%|
|26.00% | 29.46 | 1.00 | 28.46 | 3.51%|
|27.00% | 28.04 | 1.00 | 27.04 | 3.70%|
|28.00% | 26.71 | 1.00 | 25.71 | 3.89%|
|29.00% | 25.48 | 1.00 | 24.48 | 4.08%|
|30.00% | 24.33 | 1.00 | 23.33 | 4.29%|
|31.00% | 23.26 | 1.00 | 22.26 | 4.49%|
|32.00% | 22.25 | 1.00 | 21.25 | 4.71%|
|33.00% | 21.30 | 1.00 | 20.30 | 4.93%|
|34.00% | 20.41 | 1.00 | 19.41 | 5.15%|
|35.00% | 19.57 | 1.00 | 18.57 | 5.38%|
|36.00% | 18.78 | 1.00 | 17.78 | 5.62%|
|37.00% | 18.03 | 1.00 | 17.03 | 5.87%|
|38.00% | 17.32 | 1.00 | 16.32 | 6.13%|
|39.00% | 16.64 | 1.00 | 15.64 | 6.39%|
|40.00% | 16.00 | 1.00 | 15.00 | 6.67%|
|41.00% | 15.39 | 1.00 | 14.39 | 6.95%|
|42.00% | 14.81 | 1.00 | 13.81 | 7.24%|
|43.00% | 14.26 | 1.00 | 13.26 | 7.54%|
|44.00% | 13.73 | 1.00 | 12.73 | 7.86%|
|45.00% | 13.22 | 1.00 | 12.22 | 8.18%|
|46.00% | 12.74 | 1.00 | 11.74 | 8.52%|
|47.00% | 12.28 | 1.00 | 11.28 | 8.87%|
|48.00% | 11.83 | 1.00 | 10.83 | 9.23%|
|49.00% | 11.41 | 1.00 | 10.41 | 9.61%|
|50.00% | 11.00 | 1.00 | 10.00 | 10.00%|
|51.00% | 10.61 | 1.00 | 9.61 | 10.41%|
|52.00% | 10.23 | 1.00 | 9.23 | 10.83%|
|53.00% | 9.87 | 1.00 | 8.87 | 11.28%|
|54.00% | 9.52 | 1.00 | 8.52 | 11.74%|
|55.00% | 9.18 | 1.00 | 8.18 | 12.22%|
|56.00% | 8.86 | 1.00 | 7.86 | 12.73%|
|57.00% | 8.54 | 1.00 | 7.54 | 13.26%|
|58.00% | 8.24 | 1.00 | 7.24 | 13.81%|
|59.00% | 7.95 | 1.00 | 6.95 | 14.39%|
|60.00% | 7.67 | 1.00 | 6.67 | 15.00%|
|61.00% | 7.39 | 1.00 | 6.39 | 15.64%|
|62.00% | 7.13 | 1.00 | 6.13 | 16.32%|
|63.00% | 6.87 | 1.00 | 5.87 | 17.03%|
|64.00% | 6.62 | 1.00 | 5.62 | 17.78%|
|65.00% | 6.38 | 1.00 | 5.38 | 18.57%|
|66.00% | 6.15 | 1.00 | 5.15 | 19.41%|
|67.00% | 5.93 | 1.00 | 4.93 | 20.30%|
|68.00% | 5.71 | 1.00 | 4.71 | 21.25%|
|69.00% | 5.49 | 1.00 | 4.49 | 22.26%|
|70.00% | 5.29 | 1.00 | 4.29 | 23.33%|
|71.00% | 5.08 | 1.00 | 4.08 | 24.48%|
|72.00% | 4.89 | 1.00 | 3.89 | 25.71%|
|73.00% | 4.70 | 1.00 | 3.70 | 27.04%|
|74.00% | 4.51 | 1.00 | 3.51 | 28.46%|
|75.00% | 4.33 | 1.00 | 3.33 | 30.00%|
|76.00% | 4.16 | 1.00 | 3.16 | 31.67%|
|77.00% | 3.99 | 1.00 | 2.99 | 33.48%|
|78.00% | 3.82 | 1.00 | 2.82 | 35.45%|
|79.00% | 3.66 | 1.00 | 2.66 | 37.62%|
|80.00% | 3.50 | 1.00 | 2.50 | 40.00%|
|81.00% | 3.35 | 1.00 | 2.35 | 42.63%|
|82.00% | 3.20 | 1.00 | 2.20 | 45.56%|
|83.00% | 3.05 | 1.00 | 2.05 | 48.82%|
|84.00% | 2.90 | 1.00 | 1.90 | 52.50%|
|85.00% | 2.76 | 1.00 | 1.76 | 56.67%|
|86.00% | 2.63 | 1.00 | 1.63 | 61.43%|
|87.00% | 2.49 | 1.00 | 1.49 | 66.92%|
|88.00% | 2.36 | 1.00 | 1.36 | 73.33%|
|89.00% | 2.24 | 1.00 | 1.24 | 80.91%|
|90.00% | 2.11 | 1.00 | 1.11 | 90.00%|
|91.00% | 1.99 | 1.00 | 0.99 | 101.11%|
|92.00% | 1.87 | 1.00 | 0.87 | 115.00%|
|93.00% | 1.75 | 1.00 | 0.75 | 132.86%|
|94.00% | 1.64 | 1.00 | 0.64 | 156.67%|
|95.00% | 1.53 | 1.00 | 0.53 | 190.00%|
|96.00% | 1.42 | 1.00 | 0.42 | 240.00%|
|97.00% | 1.31 | 1.00 | 0.31 | 323.33%|
|98.00% | 1.20 | 1.00 | 0.20 | 490.00%|
|99.00% | 1.10 | 1.00 | 0.10 | 990.00%|

Total additional issuance: 2944.3696662216607 Prepaid interest: 91.72541640849688
