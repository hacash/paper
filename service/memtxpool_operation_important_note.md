Important notes for Hacash transaction broadcasting and txpool operation
===


> Note: All kinds of third-party services that need to submit transactions to the Hacash blockchain, such as exchanges or wallets, should read this document carefully and design the system according to the measures given, otherwise it may cause serious asset security problems such as transaction loss and duplicate transfers

Like Bitcoin, the Hacash mainnet is designed according to the [Stable and Lean Design Principles](https://github.com/hacash/paper/blob/master/tech/bitcoin_and_hacash_L1_comparison.md), stripping away all functions that are not necessarily on the mainnet and putting them on the outer layer, keeping the core of the blockchain as simple as possible. Among them, the design of Hacash's tx pool also strictly follows this principle, we will describe the design of the transaction pool, and give the handling methods in case of broadcast exceptions, mainnet congestion, etc.

### Design Description

#### 1. Block confirmation

Hacash's unique anti-51% attack technology [Beacon Tower Protocol](https://github.com/hacash/paper/blob/master/HIP/protocol/tech/PoW_of_avoid_51_percent_attack.md) protects the blockchain from malicious attacks.

In addition to analyzing and blocking malicious hashrate attacks, this protocol also stipulates the Hacash blockchain fork checkpoint: 4 blocks. This means that transactions that are highly confirmed by 4 or more blocks are secure and non-reversible, but, transactions that are less than 4 block of confirmation may be reversed.

> It is recommended that exchanges set a waiting (final confirmation) time of 4 blocks for large deposits, otherwise they may be exploited maliciously

#### 2. Transaction pools

Hacash's transaction pool is a memtxpool with the fixed  capacity, and this implementation was chosen for the following reasons:

- Setting the maximum capacity will reduce the storage burden on the device to support a normal PC or even an embedded device running a full node
- Higher performance for in-memory only processing, which can support more frequent trade insertion and sorting to support dynamic quoting of transaction fees
- The design and implementation of memory-only data structures is simple enough to support different types of full nodes, light nodes, and other types to choose their own unique implementations to support a wider variety of devices
- Considering that most of the block production under modern mining conditions comes from professional mining pools, which have sufficient development resources and often customize their own independent tx pool systems, it is simply unnecessary or even harmful for the blockchain mainnet core to provide a complex and large transaction pool implementation
- Most of the blocks are produced by professional mining pools that are stable for a longer period of time, so it would be redundant for transactions to be stored on the hard disk of a normal full node device
- Hacash does not pursue the so-called high performance of L1, the block interval is 5 minutes, there is plenty of time to process the operations of creating, broadcasting, and modifying transactions, and the possibility of exceptions such as forks, downs, or backoffs on the mainnet is very low, and it is not necessary to keep transactions on the hard disk for a long time as a countermeasure
- Pushing the responsibility for maintaining unconfirmed transactions to the outer user side facilitates the recovery of the blockchain state in the event of extreme situations
- Even if the hard disk persists the unconfirmed transaction queue, the space of this "hard disk transaction pool" is still limited and cannot be infinite, and it is also necessary to deal with too many unconfirmed transactions, and the transactions that exceed the capacity limit can still only be discarded, which does not solve the problem

This means that transactions that have already been added to the transaction pool may be removed from the mempool before the block is packaged and will not be automatically added again in the following two cases:

- Other related transactions are packaged in advance, resulting in errors such as insufficient balance, removal from the transaction pool
- The setting fee is too low, and it is arranged at the end of the mempool, and when there are too many unconfirmed transactions, it is squeezed out of the mempool

This means that in some special cases, the outer layer (the user side) will take on a greater responsibility for the maintenance of unconfirmed transactions related to itself, and cannot fully rely on Hacash full nodes to simply broadcast transactions. If you do not save the transaction and try to submit it again, the transfer may fail.

In the case of a full memtxpool (blockchain congestion), transactions with too low fees will not be able to enter the full node's unconfirmed tx queue.

### Countermeasures

Considering Hacash's 3 layer scaling mechanism, mainnet transactions will face higher fee payouts in the future, and the tx pool will likely remain full. Transactions with low fees will not be confirmed by the blockchain for a long time and may be squeezed out of the transaction pool. Third-party developers need to address this situation with the following strategies:

- You can't just store the transaction hash (TxID or TxHash), the created transaction body (TxBody) also needs to be persisted for a period of time (e.g. a week or a month) until the transaction is packaged and confirmed by the blockchain for a certain period of time
- If a transaction has not been packaged for a long time (more than 4 blocks), the transaction needs to be broadcast to the full node again in case the transaction may be squeezed out of the transaction pool
- You can directly show the complete TxBody to users in places such as withdrawal logs, and users can submit transactions by self in their wallets again (feasible when the  mainnet fee has been reduced and the blockchain is not congested)

> Warning: If you do not save the TxBody that has already been created, but create a new transaction broadcast with a new timestamp after the transaction is squeezed out of the transaction pool, there may be a situation where both the new and old transactions are packaged and confirmed (it is possible that other third-party transaction pools still save the old transaction, and when the transaction fee is reduced, it means that the exchange has transferred two withdrawals for a withdrawal operation, and the assets will be lost due to repeated spending)
>
> However, if a transaction created with the same timestamp, address, amount, etc., only the transaction fee is different, it will be regarded as the same transaction by the Hacash blockchain, and only the transaction with the highest fee will be confirmed on the blockchain, as described below

If a transaction cannot be added to the transaction pool of a full node (usually due to insufficient balance or low transaction fees), or has not been packaged and confirmed after joining, or even has been squeezed out of the transaction pool, there are two ways to deal with it:

- Wait for the network-wide fee to decrease and submit the transaction again
- Modify the transaction, set a higher fee, and submit the transaction again

Hacash allows you to modify the amount of fees for transactions that have already been submitted to the trading pool, or to resubmit transactions with a higher fee. We will describe the design and give the relevant interfaces.

### Related APIs

Hacash's transaction calculates a unique TxHash (also known as TxID) through the transaction amount transfer, receiving address, timestamp and other transaction body (TxBody) content, and the address, amount and timestamp of the two transfers are the same, which means that this is the same transaction and can only be executed once in the blockchain. However, the amount of the transaction fee (Fee field) is not included in the unique identifier of the transaction, and if multiple transactions with different fees are signed, only one transaction will be confirmed in the blockchain.

At the same time, the transaction can be signed by multiple parties, TxHash (or TxID) is divided into two types, one is the hash signed by the fee address (or called the main address) is called FeeHash, which contains the amount of the transaction fee, and the other is the hash signed by other addresses is called CommonHash, which does not include the amount of fees. Whereas, the blockchain uses CommonHash as a unique identifier for transaction identification. This means that the fee for this transaction can be set and modified freely, without the need for other parties to sign again, only the fee address to sign again and broadcast.

If you need to modify (generally increase) the transaction fee that has been broadcast but not confirmed, in addition to reconstructing the transaction with the same timestamp, transfer amount and address, setting a higher fee, and then signing the transaction and broadcasting it again, Hacash Full Node has an interface to reset the transaction fee and automatically broadcast it directly through TxHash, Fee, and PrivateKey:

- [https://github.com/hacash/paper/blob/master/service/rpc_api_doc.md#41-increase-transaction-fee-for-confirmation-get-operateactionraise_tx_fee](https://github.com/hacash/paper/blob/master/service/rpc_api_doc.md#41-increase-transaction-fee-for-confirmation-get-operateactionraise_tx_fee)

> Note 1: After a transaction has been squeezed out of the transaction pool, you need to submit the TxBody of the transaction again to modify the transaction fee and broadcast it again
> 
> Note 2: This interface can only increase the transaction fee, but cannot reduce it (it is meaningless to reduce the transaction fee setting when the whole network is congested)

