Comparison of Bitcoin and Hacash's L1 layer technology
===

Hacash focuses on improving the properties of money and the mechanism of assets based on the technology created by Bitcoin, but instead of simply copying or forking the code, it completely redevelops all parts from the ground up, and only reuses a small relevant code in the address format generated through 'base58check' that same as Bitcoin.

Hacash completely rewrites the transaction and block data structure, abandons the UTXO model, supports the mainnet interface of the channel chain payment network, and invents technologies such as readable DeFi contracts. Especially after [HIP-15](https://github.com/hacash/paper/blob/master/HIP/protocol/account_and_syntax_tree_abstraction.md) was proposed, the programmability of the Hacash L1 layer has been greatly improved, bringing more possibilities in account abstraction, asset issuance and circulation, and multi-layer scaling support.

This document will concisely compare the similarities and differences between Hacash and Bitcoin in all aspects from the perspective of public chain technology, so that readers can clearly understand what excellent characteristics Hacash inherits and carries forward Bitcoin, and what parts it iterates and innovates.

**Similar:**


1. The core layer is simple and stable

> Both Bitcoin and Hacash (hereafter referred to as both) are aiming for world currencies, not a novelty blockchain technology. So both seek to maintain the stability and high availability of the mainnet, and do not easily add experimental, unproven technologies to the core.

2. The format is the same for regular account addresses

> The algorithm for generating Hacash account addresses is the same as Bitcoin's, which is created by three processes: 'SHA256', 'RIPEMD160', and 'BASE58CHECK'. This means that the private key created by the user on Bitcoin, Hacash is directly available and corresponds to the same address. The reverse is also true, and the accounts created by Hacash are addresses that are directly available for Bitcoin. The private keys and addresses of Hacash and Bitcoin can be used in common with each other, and only one private key is needed to hold assets on both the Bitcoin and Hacash networks.

3. Trades are executed linearly synchronously

> Both do not aim at the "high performance" of the L1 layer, and both use a layered scaling approach to solve the scaling problem of the application layer. Therefore, there is no need to introduce unstable and insecure experimental technologies invented for high TPS, such as pre-broadcast and concurrency, in the execution of mainnet transactions.

4. Concise block head

> Since they both follow a simple consensus mechanism and technical system, the block head of both are very simple, with only a few fields and more than a hundred bytes. This can dramatically reduce the storage space of light node used to validate transactions in the future, and even support a large number of embedded devices.

5. Block space usage is strictly restricted

> Both believe that the "blockchain scaling impossible triangle" cannot be broken, so strictly limiting the size of data in L1 is a painful but absolutely necessary principle. Otherwise, due to the "state explosion" problem, it will become more and more difficult for ordinary users to run full nodes, and eventually degenerate to the point where all the ledgers are stored in the cloud computer room of Google or Amazon, and decentralization will exist in name only.

6. The interval between blocks is long

> In addition to the decentralization problem caused by the explosion of state data space, the pursuit of high performance of the L1 layer and the extreme compression of block interval (such as reducing to 1s) will also lead to a sharp increase in network instability due to reaching the upper limit of Internet bandwidth and hardware performance. 

7. Same PoW consensus mechanism

>   Both use a simple and reliable proof-of-work consensus mechanism that has been proven for a long time.

8. PoW fair distribution without pre-mining

> There is no pre-mined, no founding team or the foundation, and the rewards are completely open and fair.

---

**Difference:**

1. Block interval 5 minutes

> Bitcoin mines an average of one block every 10 minutes, with approximately 144 blocks mined per day. Hacash block interval is 5 minutes and mines approximately 288 blocks per day.

2. Mining algorithms are different

> Bitcoin uses the `SHA-256` hash algorithm for mining, while Hacash uses the originally invented `X16RS` mining algorithm, which can weaken the efficiency of GPU mining, prevent the emergence of ASIC equipment, and keep mining tools on CPU equipment as much as possible superior.

3. Beacon tower protocol

> Since the pure PoW consensus always faces the risk of 51% hashrates attack, and this problem is more serious for smaller new blockchain. Hacash created the " [Beacon Tower Protocol](https://github.com/hacash/paper/blob/master/HIP/protocol/PoW_of_avoid_51_percent_attack.md) " to use the power of the community to jointly fight against attackers. In theory, you need to control 51% of the hashrates and 51% of the coins at same time can launch an attack.

4. Discard UTXO

> From the beginning of its creation, Hacash was clearly based on sound money and supported more applications and DeFi functions, rather than just considering payments like Bitcoin. The UTXO model is sufficient for simple payment functions, but it is difficult to support more complex financial applications and even more difficult to implement smart contracts.

5. Layer 2 native interface

> Hacash regards currency core and decentralized large-scale payment settlement as equally important. Its original "channel chain payment network" has native interface support at the core layer, rather than Bitcoin's second layer - Lightning Network - poorly implemented under the severe limitations of existing technologies such as UTXO.

6. Readable DiFi contract

> Hacash supports broader, richer, and more flexible financial application needs than Bitcoin, and is an original revolutionary technology called [readable DeFi contract](https://github.com/hacash/paper/blob/master/tech/readability_contract_introduction.md) that is safer and more friendly to ordinary users.

7. Programmable

> Hacash can directly extend [readable DeFi contract](https://github.com/hacash/paper/blob/master/tech/readability_contract_introduction.md) into "contract syntax trees" that can perform general calculations, which brings a huge improvement in L1 layer programmability, while also being able to avoid common stack-based virtual machines (such as EVM) various aspects of weaknesses, complexity and security issues. For details, see: [HIP-15 Equity Account Model and Readable Contract Syntax Tree Abstraction](https://github.com/hacash/paper/blob/master/HIP/protocol/account_and_syntax_tree_abstraction.md)

8. Account abstraction

> Hacash's [Programmable Contract](https://github.com/hacash/paper/blob/master/HIP/protocol/account_and_syntax_tree_abstraction.md), due to the advantages of its mature technical system design, naturally supports third-party payment of handling fees, multi-sign contracts, account abstraction and other high-level functions that Ethereum has upgraded significantly.

9. L3 support

> Hacash is technically more capable of supporting various multi-layer application expansion technologies, and is not as severely hampered by technologies such as UTXO and output scripts like Bitcoin. Bitcoin will face more difficult compatibility issues and security risks in the iterative upgrade of technology. This is an important reason why Bitcoin can only be too conservative in terms of technology.

10. Mainnet can store key data

> As can be seen from asset issuance protocols such as Ordinals and BRC-20, Bitcoin not only does not support smart contracts, nor does it support structured third-party state data storage. All "Inscription Protocols" are reproductions and improvements of the "Colored Coins" technology from many years ago. Hacash can natively support asset issuance and circulation, and store more structured data for application expansion.

11. Privacy support

> Hacash described and invented multiple privacy features in the white paper, all of which can be more deeply supported in the core layer of the main network, which Bitcoin did not originally consider.

12. Three native coins

> The Bitcoin mainnet has only one native currency - BTC, while the Hacash mainnet has three PoW native currencies: HAC for large-scale payments, HACD, the world's first PoW NFT token, and BTC for one-way transfers.










