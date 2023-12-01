Conceptual explanation of Hacash's multi-layer scaling model
===

Almost half of the Hacash whitepaper is devoted to a new type of peer-to-peer payment technology: the channel chain payment settlement network. This technology is a layer 2 scaling scheme that puts the payment of the intermediate process off-chain and finally settles it on-chain. In addition, the prophetic idea of making it clear that tiered scaling is the future of blockchain comes from this 2018 white paper.

The channelchain network has been initially developed in 2021 and has been launched on the mainnet, which can be regarded as a Layer 2 network. Based on Hacash's guiding concept of hierarchical scaling, the community has also proposed a Layer3 multi-chain scaling solution, which is currently mainly promoted by the Hacash.com team.

We will compare the characteristics of the three blockchain layers of L1, L2, and L3 from multiple dimensions, and clarify the definition of each concept, on the one hand, as a summary of past community discussions, and on the other hand, we hope to lay a theoretical foundation for future discussions on Hacash in the field of scaling technology.

From the perspective of traditional Internet technology, the execution process of the blockchain system can be divided into three steps: calculation, verification and state consensus: the state after the execution of the transaction is calculated through the content contained in a transaction (transfer or contract call), and then the correctness or validity of the calculation result is verified by comparing the state data, and finally the state consensus after the transaction execution is reached by the whole network through consensus mechanisms such as mining, and a complete blockchain execution process is over. Let's examine the L1, L2, and L3 definitions in these three steps:

|Layer|Execute|Verify|Consensus|
| --- | --- | --- | --- |
|L1|✅|✅|✅|
|L2|❌|✅|✅|
|L3|❌|❌|✅|

L1 is the mainnet itself, where all computation, validation, and consensus takes place. L2 is the channel chain network, because the payment process is carried out off-chain, that is, its calculation process takes place outside the mainnet, but the final settlement and arbitration, that is, the verification of the validity of the payment, is still carried out on the mainnet, and since L2 pays for the assets supported by the mainnet, the corresponding state consensus is also carried out on the chain. The goal of L3 is to meet the greater performance of transaction business processing, so limited by the state space constraints of the mainnet, its computation and validity verification (ledger data) must be carried out outside the mainnet, but in order to take advantage of the strong security of the mainnet, L3 should embody the consensus process (state root) of off-chain state changes on the chain in various ways.

So is there still a definition of L4? It can be seen that from the above definition interpretation process, there is no such thing as L4. Because the inference from the table above means that the state consensus process of L4 does not take place on the mainnet either, which has nothing to do with the blockchain system at all.

The above definition of the L3 concept is different from Vitalik's later definition of Ethereum Layer 3. To simplify the discussion, let's refer to Vitalik's L3 as V-L3, and Hacash's L3 as H-L3.

To put it simply, V-L3 is a "fractal layer 2 network" with L2 as the settlement layer, without any correlation with the L1 layer of the main network, and the distinction between layers is more stringent. Essentially, Vitalik's conceptual correspondence between L2 and L3 is a repetition of the relationship between L1 and L2. In this way, the emergence of L4, L5 and even more layers in the context of Ethereum is theoretically possible. H-L3 is more flexible, L1, L2 and other L3s can be used as the base layer of H-L3, so there will be no more L4 layers, or all L4, L5, L6 ... and so on, all of which are collectively called H-L3 layers.

In addition to the differences in this dimension, V-L3 has more in common with H-L3:

1. L3 is a multi-chain, multi-application system, not a single chain, and it can even be called an APP-Chain

2. L3 has the flexibility to choose different consensus technologies, contract engines, or specific encryption algorithms for single, independent purposes

3. L1 is a fully decentralized + trustless layer, L2 is a semi-decentralized + trustless layer, and L3 is an optional decentralized + weak trust layer. The higher the tier, the higher the transaction processing performance and flexibility, and the lower the degree of decentralization and trustlessness

4. L2 and L3 serve different purposes, and L3 is not simply "another L2 on top of L2"

5. Most cross-chain features don't need to interact with the L1 layer

If the scalability and programmability of the blockchain system are solved through the L1, L2 and L3 three-layer structure, then the shard scaling of the L1 layer and the significance of the existence of Turing-complete smart contracts will be seriously weakened, and the advantages will not be enough to cover its shortcomings.




