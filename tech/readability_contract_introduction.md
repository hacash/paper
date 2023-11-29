# Introduction and Technical Explanation of Hacash Readable DeFi Contracts

Both the foundational layer of cryptocurrency and modern financial settlement systems require robust, secure, and user-friendly core technologies, with the key focus being on transaction data structures. Bitcoin adopts the UTXO (Unspent Transaction Output) transaction model, which is simple but comes with significant limitations. Ethereum, on the other hand, supports Turing-complete smart contracts, excelling in technical scalability, but lacking in security and user-friendliness unless individuals seek assistance from professional programmers.

The historical The DAO incident led to Ethereum's hard fork and rollback, underscoring significant security issues with smart contracts. Similar events happening within a country's monetary system would be catastrophic and unacceptable. The crypto industry, as an emerging field, has allowed Ethereum to survive primarily because of the tolerance it has received.

Hacash created the "Readable DeFi Contract" technology to balance the three dimensions of blockchain transaction data structure: security, scalability, and ease of use, which are typically hard to achieve simultaneously. By omitting some non-essential features, primarily Turing completeness programmability, Hacash has achieved excellence in both security and user-friendly usability, as well as the flexibility and robustness required for financial payments.

This document focuses on a technical understanding of the principles and transaction structure of Hacash's Readable DeFi Contracts. It introduces the characteristics and principles of readable DeFi contracts by creating two simple contract examples: 1) an atomic swap contract for HAC and HACD, and 2) an inheritance contract for HAC assets. This will assist developers in creating more diverse and customized contracts as needed.

### Hacash Transaction Data Structure

The transaction structure of Hacash can be divided into three parts, as follows:


```go
type Transaction_2_Simple struct {

	// Transaction header
	Timestamp   fields.BlockTxTimestamp
	MainAddress fields.Address
	Fee         fields.Amount

	// Transaction content form 
	ActionCount fields.VarUint2
	Actions     []interfaces.Action

	// Signature
	SignCount fields.VarUint2
	Signs     []fields.Sign

	MultisignCount fields.VarUint2
	Multisigns     []fields.Multisign
}
```

Among these, the "transaction header" data consists of a timestamp, the primary address (also the fee-paying address), and the fee amount. Hacash's transaction structure inherently supports segregated witness and third-party payment of transaction fees, whereas these two functionalities require taproot upgrades and the deployment of EIP4337 proposals on Bitcoin and Ethereum, respectively, to be implemented.

The primary implementation of Hacash's "Readable DeFi Contracts" is within the second part of the transaction structure, known as the "transaction content form." In simple terms, readable DeFi contracts are not significantly different from contracts signed in actual transactions; they both consist of combinations of payment and condition items, forming an indivisible whole transaction. Hacash utilizes a "contract interpreter" to verify and execute the submitted readable DeFi contracts. If it finds that any condition within the contract is not met (such as insufficient balance for a payer), the entire contract cannot be executed and confirmed on the blockchain (similar to the transaction mechanism in relational databases).

"Actions" represent a list of sets of payment actions or payment conditions. All parties involved in these payments, such as the payer, need to participate in the signature together (the payee does not need to sign), for the entire contract to pass the blockchain's signature verification. Subsequently, the blockchain's state machine will verify each payment condition and action within the "Actions," checking balances, block heights, and other specified conditions. If any entry does not meet the requirements, the entire contract will be rejected. In other words, all sub-entries of the contract must either succeed together or fail together; there is no scenario where some payments succeed while others fail.

Currently, "Actions" support 31 types of payment items, involving payments in HAC, HACD, and BTC, layer 2 channel opening and closing, block height conditions, etc. These payment items can be combined in any way to fulfill more diverse and flexible payment requirements.


```go
func NewActionByKind(kind uint16) (interfaces.Action, error) {
	////////////////////   ACTIONS   ////////////////////
	switch kind {
	case 1:
		return new(Action_1_SimpleToTransfer), nil
	case 2:
		return new(Action_2_OpenPaymentChannel), nil
	case 3:
		return new(Action_3_ClosePaymentChannel), nil
	case 4:
		return new(Action_4_DiamondCreate), nil
	case 5:
		return new(Action_5_DiamondTransfer), nil
	case 6:
		return new(Action_6_OutfeeQuantityDiamondTransfer), nil
	case 7:
		return new(Action_7_SatoshiGenesis), nil
	case 8:
		return new(Action_8_SimpleSatoshiTransfer), nil
	case 9:
		return new(Action_9_LockblsCreate), nil
	case 10:
		return new(Action_10_LockblsRelease), nil
	case 11:
		return new(Action_11_FromToSatoshiTransfer), nil
	case 12:
		return new(Action_12_ClosePaymentChannelBySetupAmount), nil
	case 13:
		return new(Action_13_FromTransfer), nil
	case 14:
		return new(Action_14_FromToTransfer), nil
	case 15:
		return new(Action_15_DiamondsSystemLendingCreate), nil
	case 16:
		return new(Action_16_DiamondsSystemLendingRansom), nil
	case 17:
		return new(Action_17_BitcoinsSystemLendingCreate), nil
	case 18:
		return new(Action_18_BitcoinsSystemLendingRansom), nil
	case 19:
		return new(Action_19_UsersLendingCreate), nil
	case 20:
		return new(Action_20_UsersLendingRansom), nil
	case 21:
		return new(Action_21_ClosePaymentChannelBySetupOnlyLeftAmount), nil
	case 22:
		return new(Action_22_UnilateralClosePaymentChannelByNothing), nil
	case 23:
		return new(Action_23_UnilateralCloseOrRespondChallengePaymentChannelByRealtimeReconciliation), nil
	case 24:
		return new(Action_24_UnilateralCloseOrRespondChallengePaymentChannelByChannelChainTransferBody), nil
	case 25:
		return new(Action_25_PaymantChannelAndOnchainAtomicExchange), nil
	case 26:
		return new(Action_26_UnilateralCloseOrRespondChallengePaymentChannelByChannelOnchainAtomicExchange), nil
	case 27:
		return new(Action_27_ClosePaymentChannelByClaimDistribution), nil
	case 28:
		return new(Action_28_FromSatoshiTransfer), nil
	case 29:
		return new(Action_29_SubmitTimeLimit), nil
	case 30:
		return new(Action_30_SupportDistinguishForkChainID), nil
	case 31:
		return new(Action_31_OpenPaymentChannelWithSatoshi), nil

	}
	
	return nil, fmt.Errorf("Cannot find Action kind of %d.", +kind)
}
```

You can find more code details related to these payment items and conditions at the following link: GitHub - Hacash Core.

These payment items and conditions can be represented in a user interface and readable text form, rather than relying on code logic that only professional programmers can understand. Items can be combined in any way, and users can easily and accurately review each payment and term, much like signing a business contract. Each payment can be expressed in a human-friendly natural language, providing a "what you see is what you get" experience, without potential vulnerabilities or other security issues. Next, we will use combinations of these payment items to complete two of the simplest contract examples.

### Atomic Swap Contract for HAC and HACD (DEX)

Based on the technical principles of "Readable DeFi Contracts" described above, implementing a decentralized exchange (DEX) on Hacash to allow arbitrary swaps between HAC, HACD, and BTC is especially straightforward. There is no need to write any smart contract code, similar to other public chains. Instead, you only need to develop a user interface that helps users combine several Actions and submit them to the blockchain.

Let's assume that address "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9" wants to buy 3 HACD tokens from address "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY" at a price of 120 HAC. For this DEX order, you only need two Action entries. Using JSON-like pseudocode, here's how you can represent this contract:

```javascript
{
    "timestamp": "2023-08-29 18:59:30",
    "main_address": "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9",
    "fee": "0.0001HAC",
    
    "action_count": 2,
    "actions": [{
        // Contract entry type 1, indicating that "main_address" pays "amount" HAC to "to_address"
        "act_id": 1,
        "amount": "120HAC",
        "to_address": "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY"
    },{
        // Contract entry type 6, indicating that "from_address" pays the HACD tokens listed in "diamond_list" to "to_address"
        "act_id": 6,
        "diamond_list": ["NHMYYM","WKEBKX","SSIKZM"],
        "from_address": "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY",
        "to_address": "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
    }],
    
    "sign_count": 2,
    "signs": [
        "xxxxxxxxx....(sample data)....", // Signature for this transaction from address "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
        "yyyyyyyyy....(sample data)...."  // Signature for this transaction from address "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY"
    ]
}

```

The code above represents all that is needed to complete this DEX atomic swap transaction.

Further analyzing the contract, it appears that the fees for this transaction are required to be paid by the buyer of HACD, "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9," and it is unrelated to the DEX platform. Now, you want to implement a modification where the DEX operator (assuming the service platform's address is "14ExC1ciZ5Mr5xPPLU3kKg5q3Q3Piey1xH") pays for the mainnet transaction fees and charges the buyer a fixed HAC service fee. The updated transaction contract will be as follows:

```javascript
{
    "timestamp": "2023-08-29 18:59:30",
    "main_address": "14ExC1ciZ5Mr5xPPLU3kKg5q3Q3Piey1xH", // The main address is now the DEX platform
    "fee": "0.0001HAC",
    
    "action_count": 3,
    "actions": [{
        // Contract entry type 13, indicating that "from_address" pays "1.5HAC" to "main_address"
        // (as a service fee for the matching transaction)
        "act_id": 13,
        "amount": "1.5HAC",
        "from_address": "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
    },{
        // Contract entry type 14, indicating that "from_address" pays "120HAC" to "to_address"
        // (buyer pays the seller)
        "act_id": 1,
        "amount": "120HAC",
        "from_address": "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9",
        "to_address": "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY"
    },{
        // Contract entry type 6, indicating that "from_address" pays the HACD tokens listed in "diamond_list" to "to_address"
        // (seller delivers HACD to the buyer)
        "act_id": 6,
        "diamond_list": ["NHMYYM","WKEBKX","SSIKZM"],
        "from_address": "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY",
        "to_address": "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
    }],
    
    "sign_count": 3,
    "signs": [
        "xxxxxxxxx....(sample data)....", // Signature for this transaction from address "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
        "yyyyyyyyy....(sample data)....", // Signature for this transaction from address "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY"
        "zzzzzzzzz....(sample data)....", // Signature for this transaction from address "14ExC1ciZ5Mr5xPPLU3kKg5q3Q3Piey1xH"
    ]
}
```

With just a combination of three payment items, this contract implemented on Hacash allows HAC to be purchased with HACD while paying a fixed service fee to the DEX operator, who covers the gas fees. There are no potential vulnerabilities or security risks, and the entire transaction is atomic. There is no scenario where the buyer pays HAC but doesn't receive HACD or where the trading service provider withholds funds. You don't need to send your HAC or HACD to any third-party address controlled by them. The DEX operator will only receive the deserved service fee when the transaction is fully successful.

Regarding this transaction, if any of the involved parties fail to complete the signing process, have insufficient account balances, or don't possess the specified HACD, the transaction cannot be executed, and no one incurs any losses.

### HAC Inheritance Contract

Based on the technical principles of "Readable DeFi Contracts" described above, implementing a decentralized exchange (DEX) on Hacash to allow arbitrary swaps between HAC, HACD, and BTC is especially straightforward. There is no need to write any smart contract code, similar to other public chains. Instead, you only need to develop a user interface that helps users combine several Actions and submit them to the blockchain.

Let's assume that address "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9" wants to buy 3 HACD tokens from address "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY" at a price of 120 HAC. For this DEX order, you only need two Action entries. Using JSON-like pseudocode, here's how you can represent this contract:

```javascript
{
    "timestamp": "2023-08-29 18:59:30",
    "main_address": "1853ySaM6cofnduMHcQQdR7pCQBWwJ9mVU", // Your address
    "fee": "0.0001HAC",
    
    "action_count": 2,
    "actions": [{
        // Contract entry type 1, indicating that "main_address" pays "amount" HAC to "to_address"
        "act_id": 1,
        "amount": "120HAC",
        "to_address": "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY" // Your child's address
    },{
        // Contract entry type 29, indicating that this transaction can only be confirmed on the blockchain after a specified block height
        "act_id": 29,
        "start_height": 573645  // Estimating the block height two years from now (assuming an average of one block every 5 minutes)
    }]

    // Signature data is omitted here...
}
```

This money will automatically transfer to your child after 2 years (they need to keep this transaction and submit it to the mainnet after two years). However, if you are still alive and do not want your child to receive the money at this point, what do you do? You only need to transfer the 10,000 HAC to a new address near the expiration of the 2-year period and sign a new transaction. In other words, you are essentially updating your will every two years (or any other time frame you define).

Please note that no transactions have been submitted to the mainnet at this point. This means that this contract's functionality is based on "off-chain interactions," and it will only be confirmed on the blockchain when the contract can take effect. This is a new paradigm for blockchain transactions that can significantly save blockchain space (most off-chain contracts may never be confirmed on-chain) while still achieving the functionality that would typically require code deployment and execution on platforms like Ethereum's smart contracts.

### In summary

The future holds the promise of further advancements in the world of "Readable DeFi Contracts" on the Hacash blockchain. More payment items will be supported, and the foundational protocol of these contracts will continue to evolve. This means that developers will have the creative freedom to build more robust and feature-rich "Readable DeFi Contracts." Importantly, these innovations won't come at the expense of the core principles of blockchain, including security and usability.