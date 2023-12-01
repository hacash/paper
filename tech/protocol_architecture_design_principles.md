Hacash protocol layer technical architecture design philosophy and comparison with Ethereum
===

Chapter 9 of the Hacash whitepaper, "Technical Design Principles," outlines several key principles: simplicity, compactness, controllability, and decoupling. This is the general policy that guides the construction of the Hacash architecture system as a whole, including code organization, data structure, storage layer, state layer, protocol layer, execution layer, P2P layer, interface services, miner nodes, etc.

This document focuses on discussing Hacash's technical architecture and construction principles at the protocol layer, by comparing it with Ethereum's initial design principles, combined with Vitalik's just-published article " [Should Ethereum be okay with enshrining more things in the protocol]( https://vitalik.eth.limo/general/2023/09/30/enshrinement.html) ", which summarizes Hacash's technical ideas in stages, hoping to form a set of opinions that can inspire subsequent development work and have directional significance.

Before we go into the specifics, there are two technical concepts that need to be defined first:

1. Protocol layer

It can also be called the core layer, which refers to the implementation of the blockchain system supported by the underlying functions, ordinary users cannot modify or customize this layer, and any changes require the core developers to deploy the code and notify all participants in the community to update the node software. In summary, the parts that are hard-coded within the full node belong to the core layer.

2. Contract layer

It can also be called the user layer, which refers to the user needs or business implementation of the upper layer of the blockchain system, and ordinary users can customize the development by deploying contracts, writing scripts, etc. The change of demand does not require upgrading the node software, only the user needs to update the contract. In short, the part that can meet the change in requirements without the underlying protocol change belongs to the user layer.

In the following article, we will switch between the two sets of titles of protocol layer/core layer and contract layer/user layer as needed according to the context.

### Ethereum Initial Design Principles

Since the inception of Ethereum, there has been a strong idea of trying to make the core layer as simple as possible and to meet the rich and diverse needs of users by building user-defined code on top of it. There is a strong desire to create a clean, simple, and aesthetically pleasing core layer, with as little attempt as possible to change the underlying layer to meet the needs of the upper layer of the application, and to leave almost all of this work to the upper layer and the user. Ideally, the protocol is just a generic virtual machine that doesn't contain any user business requirements, and validating a block is just a virtual machine call. This is where Ethereum's vision for a "world computer" is designed, and we can call this technical route "protocol minimization".

But Vitalik has been thinking about a different route lately, one that basically rejects the "leanest core" of the past. The article describing this line (link above) refers to it as "minimized encapsulation" and explores its design philosophy and the philosophical reasoning behind it. The goal would be to start establishing a principled framework in order to better identify possible goals in which it would be worth considering to be able to judge that encapsulating certain features could be considered.

It is worth noting that the "precompiled contract" does not belong to the concept of "encapsulation" discussed above, but belongs to the standard library or framework of the contract layer programming language, or is still in the user layer, but it has been pre-tested in terms of security and performance optimization, and its essence is still smart contract code.

### Hacash 的技术路线

Most of the topics discussed in Vitalik's [new article](https://vitalik.eth.limo/general/2023/09/30/enshrinement.html) are Hacash's technical roadmaps. To put it simply, the focus of "minimizing encapsulation" mentioned in the article is to sink the most commonly used and most important functions that can be implemented in the user layer (smart contract) to the core layer through hard-coded development and packaging, so that users do not need to deploy their own version of the implementation again, and only need to call the core layer interface with a line of code to use. But there is a word that would more accurately describe this route than "minimized encapsulation": "standardized encapsulation". Hacash mainly implements the technical route of standardized encapsulation through [equity control account] and [readable DeFi contract](https://github.com/hacash/paper/blob/master/tech/readability_contract_introduction.md).

Hacash's ultimate vision is "cryptographically sound money", which is built to meet the financial needs of currency issuance, payment, and derivative clearing and application. This asset- and value-oriented goal has completely different considerations and emphases from the purely technical narrative of the decentralized application public chain platform. Compared with the technology platform, the core layer of money and finance needs to be more stable, secure, simple, and reliable, and more intolerant of errors, which means that the latter may have constraints in terms of flexibility, such as not supporting Turing-complete smart contracts in Layer 1, or only supporting a "secure smart contract" that is strictly designed in terms of reliability in the future.

The monetary core of the world economy cannot afford to collapse. Although Hacash is currently being developed with rapid iterations, it is bound to be relatively conservative in terms of new concepts and experimental technologies that have not been tested by time, but it is not enough to be complacent and reject everything. A feasible idea is to let other innovative technology public chains in the market serve as pathfinders and test fields for Hacash, and when relevant new technologies are more mature, after rigorous analysis, learning from each other's strengths, and comprehensive consideration, we will give efficient and risk-free support, and always maintain a decentralized, stable, and sound monetary core as the judgment standard.

Unlike Ethereum's Smart Contract Virtual Machine (EVM) technology, which presents a lean core and a completely new concept, Hacash's protocol layer looks much "heavier", and because it contains a lot of hard-coded wrappers of standardized contract functionality, this compromise seems to lack some extreme technical beauty. But on the surface, Ethereum is not "light" at all, and the amount of development, complexity, and potential bug risk involved in the implementation of the EVM is far greater than the sum of all the "standard packages" of Hacash.

If we take into account the shard scaling technology that Ethereum was very optimistic about in the early stage but failed to achieve, the complexity, engineering quantity and testing process of the protocol layer will far exceed that of the "standardized encapsulation" technology, after all, the latter is more of a simple workload overlay and version release after the system. This seemingly unavant-garde approach stems from the fact that the essence of Hacash is not to be a project with an innovative technology platform as a source of value, but to pursue a monetary paradigm revolution, except that some new technologies can be used as tools.

### Comparison of advantages and disadvantages

We take the "standardized encapsulation" route represented by Hacash's [readable DeFi contract](https://github.com/hacash/paper/blob/master/tech/readability_contract_introduction.md) and the "protocol minimization" represented by Ethereum's "smart contract" The route is to compare and explore the advantages and disadvantages of the two in various aspects, so as to more intuitively show the technical philosophy of Hacash.

#### Standardized encapsulation benefits

1. The core implementation is concise

Instead of inventing a huge stack of smart contract virtual machine technologies, a very small and beautiful streamlined structure can build the key parts of the core layer, and most of the rest is just thinking about and choosing which standardized functions need to be encapsulated. The development time required is probably less than a fraction of the time required to implement a VM, let alone the subsequent complex testing work.

2. The mainnet is robust

With the same amount of development effort, the simpler the technical concept and code structure, the more it can be reviewed and tested multiple times, and the fewer potential bugs there are. Thanks to the fast, concise nature of the core implementation, multiple versions of full nodes can be developed and run in different programming languages, from security-focused strongly typed static languages to agile-focused scripting languages, which further reduces the likelihood of code errors. In the case of multiple versions of full nodes running at the same time, if one of the versions contains code errors, it will not have a catastrophic impact on the mainnet, especially when PoW is used as a blockchain consensus mechanism, which can tolerate errors on some nodes without downing the entire network.

Another side advantage of multi-version nodes is that test versions with new features or packages can be developed very quickly in dynamically typed languages, and community discussions can take place directly on top of the experimental code that is already running, thus eliminating the excessive cost of communicating technical concepts to explain and understand. In fact, while the development community is still very small, Hacash already has full-node versions in both Golang and Rust.

3. Experience friendliness

Due to the simplicity and prescriptiveness of standardized encapsulation technology, the underlying interface of a readable financial contract is as simple as a piece of data in JSON format with well-defined fields, and users can directly read and understand what is happening in the transaction. To put it simply, a readable financial contract is not much different from a contract signed in real economic life: it is a combination of payments and conditions that form an inseparable whole. Users can construct and understand each clause of a readable financial contract just like they would with a normal text business contract.

If combined with natural language AI technology, users can automatically generate the corresponding readability contract by inputting some text descriptions of transaction intentions or directly dictating requirements, and can check the correctness of the contract in the most simple and intuitive way close to natural language.

4. User Security

A smart contract is essentially a virtual machine environment that requires code to interact directly, and even though some protocols have developed web interfaces for ease of use, they also pose a centralized trust risk to UI developers. The external interface of a readable financial contract is a transaction that contains a list of transactions, which does not require programmer programming and does not rely on code written by a third party, so it will not introduce corresponding bugs and vulnerabilities.

Considering the irreversible nature of blockchain transactions, it is much safer to be able to manage a large amount of money and property independently in the operating procedures that you can intuitively understand, rather than through the help of a third party. Every link of contact with the outside world is a serious risk point.

5. Easy auditing

Checking the correctness of the contract does not require a third-party professional code auditor, does not need to understand the code, and checks the contract like reading an ordinary text contract, the entries are clear, and what you see is what you get. This can save a lot of audit fees and reduce the psychological burden of worrying about the insecurity of transactions.

6. Low resource usage

Transactions, blocks, and state space take up very little space. To achieve the same contract function, the block space required for a readable contract may be more than a dozen bytes, while a smart contract often needs to deploy a set of contract code, which requires much more space. In the long run, it will be easier to bring about the problem of "state explosion", which will seriously affect the decentralization of the blockchain network, and eventually become a "computer room chain".

7. Low transaction fees

Because the readable contract does not have a complete set of basic support data for the contract code, the same block space can contain more transactions with the same demand, and due to the fee competition mechanism of the mainnet, the transaction fee will be lower under the premise of the same actual demand processing volume.

8. Low combinatorial risk

The DeFi Lego of smart contracts has the problem of compositability vulnerability, assuming that the probability of a vulnerability in each contract module is 10%, then the theoretical reliability of the combination of ten contracts is reduced to only about 35%, which is unacceptable. Readable contracts are designed to be decoupled and orthogonal at the renewal level, with any combination of entries that do not pose a higher risk of collapse.

9. Support "off-chain gaming"

"Off-chain games" save block state space. Readable contracts can sign multiple contracts with assets associated with each other, and these contracts can be guaranteed by a number of mutually exclusive entries, so that certain financial payment guarantees can be reached without actually committing all of these transactions on mainnet to save block state space. Vitalik has written about this technology.

10. Low communication costs

The unification of protocol standards can greatly reduce communication costs. Mainstream financial needs are met by a common set of mainnet code, and participants don't need to struggle to understand the differences and details between the various smart contracts, or have a professional programmer translate what exactly does inside the contract. All communication takes place efficiently in the same conceptual context.

If you want to understand the technical principles and transaction structure of Hacash Readable Financial Contracts at a technical level, you can check out the document: [Introduction and Technical Notes of Hacash Readable DeFi Contract](https://github.com/hacash/paper/blob/master/tech/readability_contract_introduction.md), which explains by creating two simple contract examples.

#### Protocol Minimization Benefits

Although the "protocol minimization" technology represented by smart contracts has the shortcomings of all parties, it still has some advantages, which are mainly reflected in three aspects:

1. The core development goal is more singular

While the smart contract virtual machine stack is large and complex, it can be seen as a single goal. Once the build is complete and mature, it can continue to run with little to no major modifications, which means that in a sense, the core development team of the VM can "lie flat" and only need to work on some of the performance optimization work on a day-to-day basis.

In the early days, readable contracts require relatively continuous standardization discussions and package development, and although the frequency of such new work will be reduced after most of the requirements are met, it still needs to be continuously updated in theory. This means that readable contracts need to rely on a more active and continuously active core development group, and we can't conclude whether the need for a more active core technical team in today's crypto industry is a good thing at the market level.

2. Meet user needs faster and more comprehensively

The requirements that can be met by readable contracts are more general and standardized, and if there are more special customization functions on the user side, it is more straightforward and faster to write the contract code by yourself than to submit the requirements and wait for the core development team to discuss the implementation.

Perhaps in the future, Hacash can more fully adapt to the unique needs of users through more flexible and custom-coded "secure smart contracts".

3. The evolution of community projects is richer

Due to the Turing completeness of smart contracts, the functions that can be realized theoretically are almost unlimited, which brings a rich and borderless free construction and adoption of various projects, and the public chain that supports smart contracts can grow into a more colorful ecosystem, and there is an opportunity to give birth to some unexpected new types by trying a variety of possibilities. Comparatively speaking, the ecology constrained by the strong objective nature of readable contracts (which basically refers to monetary and financial needs) may seem monotonous, although the latter is no less important than the former.

---

With the continuous evolution of blockchain and encryption technology, some of the above problems may be solved, and different technical routes may gradually move closer to each other through continuous changes, optimizations and supplements in their own shortcomings, and finally form a situation where different paths lead to the same end. However, this does not prevent us from understanding the key considerations and philosophical routes of different technical routes from their respective perspectives in the principles of initial design.

