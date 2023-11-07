HIP-15 Equity account model and readable contract syntax tree abstraction
===

The Hacash white paper describes a new account model called [Hierarchical Equity Control Account], which is a more flexible and powerful account type than multi-signature management and can adapt to the organized and institutionalized asset management methods of the modern financial system.

"Account abstraction" is a concept that has received a lot of attention in Ethereum recently, and has even become the next major upgrade of Ethereum. This upgrade exists because Ethereum was mainly aimed at the "world computer" when it was created, focusing on the design of the "computing" part and realizing Turing-complete smart contracts. but didn't think much about in terms of account models and control permissions, or even ignored it.

The core of the so-called account abstraction is the abstraction of asset management permissions. The principle is to unify "account" and "contract" at a higher logical level, so that both become the same first-class citizens in the system, or in other words, they become the same thing, except for the different ways of verifying at transaction permissions. some are directly through the traditional elliptic curve signature verification method, while others are changed to custom verification logic programmed in contract code. Instead of like now, the account system is divided into two types: EOA (Externally Owned Accounts) and CA (Contract Accounts).

Technically speaking, the purpose of the account abstraction upgrade that Ethereum wants to achieve is to achieve the function of hierarchical equity control accounts similar to Hacash. Combined with Hacash's [Readable Financial Contract], as a major upgrade of Ethereum, "account abstraction" can be naturally supported within Hacash, and it is based on the most mature technical architecture, rather than patching technology that requires compatibility.

As a result, HIP-15 was proposed, with the goal of conceptually upgrading the readable financial contracts that Hacash already had and available, called a "contract abstract syntax tree". The interpreter of the contract changed from a one-dimensional combined financial The interpretation of the contract is upgraded to the interpretation of the program logic syntax tree, which can be a tree structure. The readability and simplicity of the contract continue to be retained, but a series of `action` or `OP_CODE` for logic control and basic operations are added, which can directly upgrade the readable financial contract to a "syntax tree" that can perform general calculations ". This will bring about a huge improvement in contract programmability, while also avoiding various weaknesses, complexity and security issues based on common stack virtual machines (such as EVM).

In a sense, this becomes a concept of great significance in terms of blockchain programmability: human-readable financial contracts and programmability to perform general-purpose calculations are consistently abstracted and perfectly unified.

More importantly, this technology also solves multiple needs within the Hacash system:


1. This prevents the original equity control account function from being hard-coded into the Hacash core and can be implemented through user-side programming, fully customizing the control logic and parameter settings.


2. The goal of "account abstraction" was immediately achieved, unifying ordinary accounts and contract accounts, allowing more business scenarios that cannot be satisfied under Ethereum's split account model to be supported.


3. The programmability of Hacash's readable financial contracts has been greatly improved, allowing it to support more financial businesses. Some more frequent requirements can be tested and met in advance, and then used as the basis for decision-making and execution of the "minimal viable enshrinement" of the Hacash core layer.


4. It can more flexibly support more expansion goals, such as various types of Rollup or L3 expansion, or asset issuance at one layer, such as the HRC-20 protocol.


At the same time, HIP-15 only requires minor improvements based on existing readable financial contracts and maintains forward compatibility. The simplicity and decentralization of the mainnet will not be affected.

Here is a simple contract code that demonstrates "account abstraction" for equity control addresses:

```js

/* Define contract interpreter compatible version */
pragma vm 1.2.31

/* Define an equity control contract/account. Under the concept of "account abstraction", the contract is the account and the account is the contract. They are the same thing. */
contract MyMultiControlAccount {

	/* Contract constants */
	const minimum_permission_ratio = 51 // minimum percentage of control
	
	/* contract variables */
	var address recovery_account = "1qkdhtn......" // Recovery account for emergency hedging


	/* Only called once during deployment */
	constructor(address re_acc) {
		recovery_account = re_acc // Set up recovery account
	}

	/* Authentication permission, called every time this address needs to be verified. */

	/* Equity management account authority verification control logic is executed every time assets need to be used. Four addresses control the assets of this account through different voting rights. */
	permit() {
		var votes = 0
		// reqsign, this function represents to verifie the signature of address, and the signature represents a positive vote.
		if(reqsign(addr1)) {
			votes += 30 // votes add
		}
		if(reqsign(addr2)) {
			votes += 30
		}
		if(reqsign(addr3)) {
			votes += 25
		}
		if(reqsign(addr4)) {
			votes += 15
		}
		if(votes < minimum_permission_ratio) {
			// Insufficient votes, authentication failed
			return false 
		}
		// With more than half of the signature votes, the authentication is passed.
		return true 
	}

}

```

The invocation and execution of contracts inevitably require pre-stored contract codes on the blockchain. If not controlled, the large occupation of state space will become a serious flaw affecting the decentralization of the mainnet. The "state explosion" problem of Ethereum's full nodes is irreversible. It is expected that in many years, Ethereum will become a "computer room chain" that only runs and stores ledgers in Google or Amazon's cloud databases.

There are two solutions for this problem:


1. Strictly control the contract's occupation of state space. For example, each block only allows 10kb of contract space, and squeeze unimportant data and calculations into 2- or 3-layer expansion facilities.


2. Can refer to the mechanism design of "renting" and "releasing" state space in Nervos blockchain.






