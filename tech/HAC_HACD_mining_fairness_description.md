HAC and HACD mining description of fairness
===

This document is not a comprehensive and in-depth detailed explanation of the Hacash mining mechanism, nor is it an operation instruction manual for teaching mining, but an explanation of the fairness design in the mining reward and bidding mechanism of HAC and HACD.

## **HAC mining**

The HAC block reward 6 years to increase, then 60 years to decrease, and finally remains the same forever. Let anyone who is willing to participate in mining have sufficient time and opportunity to invest a market fair cost to get their due share of the currency.

Hacash mines a block every five minutes on average, and initially rewards miners with 1 HAC per block, and 288 blocks are mined every day, that is 288 HAC per day.

The growth of the block reward is based on the Fibonacci numbers, which is divided into three stages: fast increase, slow decrease, and remain unchanged.

The increase phase is 6 years: 1 HAC per block in the first year, 1 HAC per block in the second year, 2 HAC per block in the third year, 3 HAC per block in the fourth year, and 5 HAC per block in the fifth year, and increase to 8 per block reward in the sixth year. During the first 6 years, the mining reward has been increasing, from 1 to 8.

The decrease phase is 60 years: from the 7th year, the mining reward is still 8 HAC per block for 10 years. In the 17th year, the reward is reduced to 5 HAC, which will be maintained for ten years. In the 27th year, the reward will be changed to 3 HAC, which will last for 10 years. It dropped to 2 HAC in the 37th year and 1 HAC per block in the 47th year. Years 57 and 67 are still 1 HAC per block reward.

Remaining unchanged stage: Beginning from year 67, the block reward enters an eternal and unchanged era, 1 HAC per block, forever.

In the first 66 years, 22 million HAC block rewards can be mined. The percentage of coins released by mining each year in the first 66 years of release is as follows:

|**Year**|**HAC per block**|**released of year**|**total released**|  
|:----|:----|:----|:----|
|1|1|0.4555%|0.4555%|           
|2|1|0.4555%|0.9111%|           
|3|2|0.9111%|1.8222%|           
|4|3|1.3667%|3.1889%|           
|5|5|2.2778%|5.4667%|           
|6|8|3.6445%|9.1112%|           
|7|8|3.6445%|12.7557%|              
|8|8|3.6445%|16.4004%|           
|9|8|3.6445%|20.0448%|           
|10|8|3.6445%|23.6893%|           
|11|8|3.6445%|27.3338%|           
|12|8|3.6445%|30.9783%|           
|13|8|3.6445%|34.6228%|           
|14|8|3.6445%|38.2673%|           
|15|8|3.6445%|41.9118%|           
|16|8|3.6445%|45.5563%|     
|17|5|2.2778%|47.8341%|     
|18|5|2.2778%|50.1120%|     
|19|5|2.2778%|52.3898%|     
|20|5|2.2778%|54.6676%|     
|21|5|2.2778%|56.9454%|     
|22|5|2.2778%|59.2232%|     
|23|5|2.2778%|61.5011%|   
|24|5|2.2778%|63.7789%|    
|25|5|2.2778%|66.0567%|   
|26|5|2.2778%|68.3345%|   
|27|3|1.3667%|69.7012%|  
|37|2|0.9111%|82.9125%|   
|47|1|0.4555%|91.5682%|   
|57|1|0.4555%|96.1239%|   


By comparison, Bitcoin has mined about 92% of the 21 million total.

## **HACD mining and bidding**

There are two steps to obtain HACD diamonds, first mine and then bid: first, invest mining computing power to mined the legal HACD literal value with a given difficulty; second, use HAC bidding to obtain the only HACD confirmation opportunity in a 25-minute cycle .

**mining**

The mining difficulty of HACD adopts a unique mechanism that only increases and does not decrease. The difficulty adjustment is only related to the number of diamonds that have been minted, and has nothing to do with how many people participated or scale of hashrates in the mining during the same period.

According to the statistics of early miners, the initial mining difficulty is as difficult as mined one HACD in an average of 6 hours on an ordinary household consumer computer. From the algorithm point of view, this initial difficulty is not deliberately selected and human set, but is determined by the objective basic difficulty of the HACD literal hash generation algorithm.

In terms of algorithm details, HACD's mining difficulty adjustment mechanism is divided into five parts, which are mixed for the calculation of the difficulty standard:

1. The DimondHash algorithm invented by HACD has a basic difficulty. It takes about 6 hours for an ordinary home computer to mined one, and the subsequent difficulty adjustment will take this as the difficulty base.
2. The number of hashes increases about half a year for every 8192 HACD minted, from 1 hash calculation in first to 16 hash calculation in 8 years, and remains unchanged thereafter. Equivalent to the difficulty of computing power will increase by 16 times in the first 8 years;
3. It takes about 2 years for every 42,000 HACD to be minted, and the mining difficulty becomes twice the current mining difficulty, that is, the difficulty factor × 2 . But then the multiplier for each difficulty adjustment will decrease slightly by a factor of less than 2, until it finally decreases to 1 after 64 years. The specific changes are likely to be: 2.00, 1.94, 1.88, 1.83, 1.78, 1.73, 1.68, 1.64, 1.60, 1.56, 1.52, 1.49, 1.45, 1.42, 1.39, 1.36, 1.33, 1.31, 1.28, 1.25, 1.23, 1.21, 1.19, 1.16, 1.14, 1.12, 1.10, 1.08, 1.07, 1.05, 1.03, 1.02, The final coefficient is 1, then the difficulty does not increase。
4. About 56 days after 3277 HACD are minted, the difficulty coefficient will be adjusted by 'one byte', that is, the left byte of the difficulty hash will be reduced by one. This kind of difficulty adjustment changes according to an exponential curve. At present, it is impossible to calculate the specific multiple, and the increase in difficulty in the early stage is not large, and it is not even easy to detect. However, with the increase in the number of HACD minted, the difficulty will increase, and the difficulty increase will gradually become significant in the middle period of mining, which will surpass the mining difficulty of Bitcoin. In the later stage of mining, The increase is increasing until it exceeds the computing power of all computing devices on the planet. In the final stage, the mining difficulty will increase to a mathematical limit, to the point where it is difficult to dig out a diamond even if the energy of the sun is exhausted.
5. Since the literal value of HACD is composed of 6 letters, the total upper limit is 16,777,216. The randomness of the hash calculation leads to the fact that the confirmed literal value of diamonds can be mined repeatedly, so this calculation is also invalid. This will compress the space for effective mining. Although it has little effect on the difficulty in the early stage, when half of the HACDs are minted, the difficulty will increase to 2 times, and then the remaining half of the HACDs will be minted, difficulty increase by 4 times. and then minted in half, and increase the difficulty to 8 times. Every time half of the remaining diamonds are dug up, the difficulty will double, and so on, until the last batch of diamonds is dug up, at this time, almost all the miners have a high probability of mined the duplicate literal value, and mined new valid HACD characters will be very difficult, and the difficulty will grow to the extreme of the impossible.

The combination of the above five dimensions makes the adjustment of the difficulty of diamond mining difficult to show with a simple parameter curve, and can only rely on mining tests and statistics with miners. Unless someone with extremely high mathematical ability and is a programmer at the same time can convert this compound difficulty adjustment mechanism into a mathematical formula, and finally make a chart for everyone to view.

Due to the uncertainty of mining computing power investment and the continuous development of new technologies, it is impossible to determine the time when diamonds will stop mining due to excessive difficulty in the future, or the number of diamonds that can be excavated. it's have a possibility of restarting mining activities that have been stopped for a long time after the introduction of new and more efficient mining hardware. The only certainty, the mathematical mechanism guarantees that the diamonds will never be finish minted. The difficulty of mining the latter part of the diamonds will increase to the point where, using the full computing power of Bitcoin, one cannot be mined every day, and the difficulty will continue to increase until it reaches the limit of infinity.

**bidding**

Mining HACD through computing power does not mean that the HACD has been obtained, and it needs to be confirmed by bidding. Each HACD has an automatically increasing serial number, starting from 1, each time a HACD is minted, the next serial number of HACD is automatically incremented by one.

After mined, the mining parameters need to be wrapped in a transaction and submitted to the transaction pool of the Hacash mainnet for confirmation. HACD can only be included in blocks that are divisible by 5 (that is, the block height number ends with 0 or 5), but it can also not contain HACD, for example, the hashrates is too low to mining or no one mining. That is to say, a 25-minute bidding period is used. No matter how many HACD are excavated in this period, only one HACD with the highest bid can be confirmed in the end, and other HACD with the same serial number are automatically invalidated. Only the HACD whose bidding is successfully finalized need to pay the bidding fee, and the bidding fee issued by other invalid HACD will not be deducted.

After one HACD is confirmed, everyone will restart mining again, and the next round of HACD mining and bidding will be conducted fairly from scratch. Since an average of 288 blocks are mined every day, the daily releases of HACDs is at most 58 diamonds, 1740 HACDs per month, and about 21,000 HACDs per year. Until the mining difficulty increases and the output starts to decrease.

and the lower the cost of computing power. However, the bidding cost cannot be determined, it is completely determined by the bidding competition of other market participants, and is affected by the overall market conditions.

The bidding mechanism determines the maximum release HACDs that can be minted every day and every year, controls the release speed of HACD, and avoids being seized by big miners or "scientists" in a short period of time with capital or information advantages for most of the chips in the early stage. Players always have the opportunity to invest computing power and bidding fees to obtain the desired HACD according to the market value, so as to achieve long-term fairness in distribution.