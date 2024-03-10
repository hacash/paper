HACD additional note for exchanges
===

>
> Before reading this document, you need to consult the [RPC API documentation](https://github.com/hacash/paper/blob/master/service/rpc_api_doc.md) for Hacash Full Node as a basis for technical understanding.
>

HACD (also known as diamond) is an NFT (non-fungible token), but it can be divided and traded on the exchange at will like FT (fungible token), and it is fully adapted to the exchange program of deposit, withdrawal and trading developed for FT tokens on ordinary blockchains, and only needs to do a little additional development (query one or two more interfaces). This document will detail the conceptual understanding and considerations of exchanges or other third-party platforms when accessing HACD.

This document assumes that the developer is already familiar with the Hacash interface documentation and how to use the RPC interface to complete the usual integration development work such as new account creation, HAC transfers, or block transaction scanning, but there are some conceptual or technical hurdles in the integration of HACD.

## Basic understanding

There are three native PoW currencies on the Hacash chain: HAC, BTC, and HACD. Among them, HAC and BTC are FT (fungible), while HACD is NFT (non-fungible), which is divided into indivisible coins, and its transfer on the Hacash chain must also be in integer tokens, and the number with decimal places such as 0.1 or 2.5 is not supported.

HACD uses a non-repeating six-digit letter as a unique mark, called a literal value or diamond name, and is randomly combined from the 16 characters 'WTYUIAHXVMEKBSZN', with a maximum total of 16^6 = 16777216. Each one is unique and indivisible.

On the Hacash blockchain, unlike the usual FT tokens (such as HAC), which directly use digital transfer methods, HACD transfers must be made through a literal value list, that is, a comma-separated 6-digit letter list, such as 'WTYUIA' or 'AHXVME, KBSTZN' and other strings, so as to accurately transfer the ownership of HACD NFT, similar to Ethereum's transfer of NFT by numeric number.

HACD will store the address of the owner in the state data of its blockchain, which will be used to record the ownership of HACD.

## Technical docking

#### Recharge

When ordinary FT tokens are transferred or deposit to the exchange, it will only be reflected in the transfer of the amount of tokens represented by a number, while the deposit of HACD cannot be achieved with only one number, and must have a complete list of names, so that the blockchain system can accurately change the ownership of these NFTs. When the exchange scans the block transaction, it will get a 'diamond' field from the HACD deposit transaction, which is a string value, which is a list of HACD names filled into the exchange address, separated by commas, such as 'WTYUIA' or 'AHXVME,KBSTZN,WTYUIA'.

At this point, by using the split function to parse this string, you can get a list of HACD names and quantities that are filled into the exchange. Then, as with FT deposits, after several block confirmations, the amount of HACD deposited is increased to the customer's balance sheet, and the deposit is completed.

> [Additional Development 1]: The exchange needs to record the names of these deposits separately, and maintain a literal list of all HACD owned by the exchange, or a list of HACD owned by the corresponding address, for subsequent transfers or withdrawals.

#### Transactions

After the deposit amount is increased to the customer's account balance, the trading within the exchange is no different from FT, and can be divided at will, and the decimal transaction is supported, such as pending orders in the order book such as 0.1, 0.003 or 12.57 to buy and sell.

After trading, the customer's HACD account balance can also support decimals, such as 127.38 HACD.

#### Withdrawals

Since HACD can only be transferred in the form of integers on the Hacash chain, customers only support integers and not decimals when withdrawing HACD. The minimum is one, and the maximum is two hundred.

Therefore, when the customer's HACD balance is less than one piece, the withdrawal operation cannot be made. Withdrawals can only be made when the client buys at least one HACD.

When a customer applies for a withdrawal, it is not possible to transfer money directly through a number, but must randomly take out the literal value of the corresponding withdrawal amount from the list of HACD names maintained by the exchange, and then use these withdrawn names to construct a transfer transaction of HACD and withdraw a specific HACD to the customer.

> [Additional Development 2]: When withdrawing, the corresponding amount of literal values will be randomly taken out from the list of all HACDs owned by the recorded exchange, transferred to the customer, and these literal values will be removed from the total balance list of the exchange. (Not transferable to the name of the diamond that has been transferred to another account)

#### Account aggregation and cold wallet maintenance

Whether it is the deposit address generated by the exchange separately for each customer, or the aggregation wallet and cold wallet address, which do not participate in the mining and staking of HACD, etc., the list of HACD balances and names owned by each address can be parsed and constructed from the scanned block transfer records (adding or removing the six-digit literal list).

When you need to collect a customer's deposit to a hot wallet, or transfer from a hot wallet to a cold wallet, you can construct a transfer transaction from each address maintained within the exchange and the list of HACD literals it owns.

When manually withdrawing HACD from a cold wallet to a hot wallet, you can go to [Blockchain Explorer] (https://explorer.hacash.org/) to view the list of HACD names in the cold wallet address, and then manually initiate the transfer.

> [Convenient Data Api] If this kind of aggregation and hot and cold maintenance needs to be automated, then the block explorer provides an interface to query the diamond literal value owned by a single address:  [https://explorer.hacash.org/api/ranking/diamonds?address=1QDc1twwVy3acuftAv3GuNnKwxopYi9VLb] (https://explorer.hacash.org/api/ranking/diamonds?address=1QDc1twwVy3acuftAv3GuNnKwxopYi9VLb) 

The return values are as follows:

```js
{
    diamonds: "KEATNHZVHIVYTBSVUWEUBUUWHZNVMIHNWSVUWUXNXVASYXTVTNSBHBUWMYUM",
    ret: 0,
}
```

> [Tip 1] Since there may be tens of thousands of diamonds under an address, in order to compress the amount of data to maximize the efficiency of interface access, the name list is not separated by commas, but directly concatenated by 6 digits, and the string index can be directly iterated with 'i += 6' during the sharding process.
>
> [Tip 2] This API returns the data after 4 block confirmations. That is, to get a list of HACDs, the content will lag by 4 blocks, either in or out. There are plans to directly support access to the list of diamonds under the address in a future full node release.

## Other considerations

1. The Hacash system limits the maximum of 200 per HACD transfer, so whether it is a user depositing or withdrawing, the maximum is 200 per transaction. However, the order book within the exchange is exempt from this restriction.

2. Using the block explorer interface is only a temporary stake measure, and it is recommended that exchanges maintain their own list of HACD literals by scanning transactions directly. This third-party interface may change in the future or may no longer be available.

3. When constructing a HACD transfer transaction, you can use another address as the on-chain gas fee payment account, and you don't need to have HAC at the same time to transfer HACD out. Exchanges can use hot wallets or dedicated gas fee accounts to pay gas fees for all HACD transfers.



