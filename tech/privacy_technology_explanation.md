Hacash Privacy Technology Design Principles
===

Long before the pursuit of uncensorable cryptocurrencies based on blockchain technology, the primary goal of cryptopunks was privacy, more precisely through technical means to enable peer-to-peer encrypted communication. However, due to the technical characteristics of the blockchain's public ledger, anyone can view all transactions at any time, and the existing privacy technology cannot be directly used to encrypt the transaction content and ledger updates, and privacy protection has become urgent again.

Since the creation of Bitcoin in 2008, there have been various projects that have tried to implement privacy protection on the blockchain, each with different technical characteristics and different popularity. In its 2018 white paper [Chapter 7], Hacash outlined the need for privacy within the monetary system and created a new privacy technology, "pre-deferred payment".

Before introducing the relevant technical architecture and implementation of Hacash, it is necessary to discuss the characteristics and constraints of privacy in the blockchain system, so as to obtain some basic consensus, so as to better understand the underlying reasons of Hacash's technical roadmap.

### 1. There are trade-offs to achieve

Privacy technology is not a one-size-fits-all or cost-free technology, and many aspects need to be sacrificed in exchange, such as convenience of experience, decentralization of key locations, operational efficiency of mainnet, or complexity of technology development. For example, some privacy technologies require the payee to sign online in real time, some rely on some kind of "initial key" for multisig management to control the privacy state, and others need to periodically wait for the online interaction of each "ring signer". These experiences and technical troubles are a serious obstacle to the adoption of these privacy technologies.

The complexity of technology development often leads to the fact that the founding team must have sufficient interests to invest in R&D and support operations, and naturally controls most of the permissions of its privacy public chain through "token governance" and other forms, which is almost centralized, which is contrary to users' pursuit of uncensored and uncontrollable privacy.

### 2. Non-global rigid requirements

Privacy is not a rigid requirement that must be fully guaranteed at all costs, and even sometimes we don't want to take too many troublesome additional steps for something that is not important. For example, the average person doesn't engage in a "privacy technology interaction" for more than half an hour to keep anyone from knowing that they bought a cup of coffee in the morning. Most people make the right trade-offs in terms of experience and cost, and few are desperate extremists.

### 3. It is highly restrictive

The specificity of privacy technology and the directivity of functions seriously limit the realization and richness of the technical requirements of the mainnet, and basically nothing can be done except transfers. This greatly restricts the application scenarios of blockchain, reduces the number of potential users, and faces the risk of being seized by other more versatile and flexible blockchain platforms at any time.

### 4. Difficult to recover

The cultural goals and technical complexity of the privacy public chain make it more difficult to track and recover the network once it goes wrong, and it is easy to fall into complete failure. For example, the unexpected token issuance caused by a vulnerability in the system is likely to be unverifiable and undetectable externally, and when it is reflected through changes in market prices, the entire system is already flooded with inflation, and it is too late.

---
 
Based on these arguments, Hacash's privacy technology design will be guided by the following principles:

### Thought Layer

1. Privacy optional

Hacash's privacy technology is only one part of the overall mainnet technology, which is a subset of readable financial contracts, and is not built from the bottom with specific privacy technology. This makes the core layer of Hacash not constrained by privacy technology, thus giving users the freedom to choose between privacy and convenience.
	
2. Multiple combinations

Hacash doesn't support just one privacy technology, but multiple at the same time. With readable financial contracts, it is even possible to employ multiple privacy schemes within the same transaction. This greatly enriches the application scenarios of privacy technologies, and may combine and derive unpredictable requirements adaptation.
	
3. Middleman scheme

Hacash is not an extreme admirer of technology in terms of the white paper, and will not completely reject social and business cooperation in certain controllable scenarios. For example, in the chapter on channel chain settlement networks, the possible professional "payment network access service providers" are discussed, and the privacy chapter also includes deferred payment privacy technologies that need to cooperate with intermediaries/service providers.

4. Low risk and high efficiency

Hacash's world monetary goals have made its choice of technology conservative and prudent, and it is not possible to actively adopt trendy technologies without long-term validation, mainly to reduce the risk of affecting the core layer of the currency. In addition, the monetary target also has more stringent requirements for the operation efficiency and reliability of the network. This will undoubtedly affect the market's assessment of Hacash's value from a technical perspective.
	
### Technical layer

1. Basic mixing

This is the most basic and easiest category of privacy technologies to implement, supported by Bitcoin, Ethereum, and other smart contract public chains, and has little to no negative effect on the operation of the blockchain. However, its degree of privacy protection is limited, and it basically depends on the size of the participants, and will limit and unify the number of transfers. The more people who participate in the mix, the greater the privacy effect, but the more interaction and waiting is required.

2. Pre-deferred payment

The coin mixing technology blurs the direct connection between the sender and receiver of funds from the "space" (the order of receipt and payment is disrupted at the same time), and the pre-deferred redemption technology cuts off the whereabouts of the funds from the "time", and the principle is to use an intermediary to transfer out immediately but delay the collection of funds, so that the final recipient of the transaction cannot be known for a period of time. Again, there are no side effects to this privacy technique, but you need to find a middleman or service provider with a large enough capital to be able to withstand a longer redemption lock-up period.

3. Encrypted channel chains 

For payments made through the channel chain settlement network, all transaction data is disseminated off-chain and not made public, which can also protect our privacy to a certain extent. However, the payment channel node may be required to provide the identity information of all connected customers, and there is a possibility that the data of each consumption may be leaked. The encrypted channel technology can hide the payment data from all intermediate payment nodes, so as to strictly protect the privacy of end customers.

4. Adapt to more privacy technologies

The core architecture of Hacash's blockchain technology is mature and flexible, and it is possible to mount and integrate more proven and mature technologies in the future, and privacy is an important part of this. In other words, although Hacash is not a public chain that only focuses on privacy, it has a larger goal, but it can also graft the mature and reliable open source privacy technology that has been successfully verified in other specialized privacy public chains at any time.

