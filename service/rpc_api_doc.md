Got this english version doc from: https://github.com/jcb55/jcb-hacash/blob/main/rpc_api_doc.en.md , thank you [jcb55](https://github.com/jcb55) ^_^.

Hacash full node RPC API documentation
===

This document contains the api interface specification and examples for block scanning, transaction transfer query, account balance query, block diamond information query, creation of new accounts, creation of transfer transactions, etc. It is part of the development of the Hacash cryptocurrency nd facilitates the blockchain explorer browser and exchange functionality.

This document will provide a sample test interface (currently available, but deprecated), which can help you view the content returned by the interface immediately, or temporarily use it for testing and debugging. In a production environment, please configure your own server and set up a full node to ensure the stable availability and security of the interface. See [hacash.org](https://hacash.org/) for the tutorial of building a full node.

After downloading the latest version of the full node program and starting the program to synchronize all blocks, you can enable the RPC API service by adding the following configuration in the configuration file (hacash.config.ini):

```ini

[service]
enable = true
rpc_listen_port = 8083

```

The above configuration `enable = true` means to enable the RPC interface service, `rpc_listen_port = 8083` means that the listening http service port is 8083.  Note that there is a deprecated rpc protocol which was configured on port 3338, and it is possible to configure both at the same time, however this will not be maintained.

Visit `http://127.0.0.1:8083/` to verify operation, if all is good you will see:

```json
{
    "ret": 0,
    "service": "hacash node rpc"
}
```

### Quick start

Before documenting the detailed specification we can show a few examples, with the full node running, visit [http://127.0.0.1:8083/query?action=balances&address_list=1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS](http://127.0.0.1:8083/query?action=balances&address_list=1AVRuFXNFi3rdMrPH4hdqSgFrEB), this is an api query for the balance of an account address. For example upon success, it will return:

```json
{
    "list": [
        {
            "diamond": 1016,
            "hacash": "ㄜ1,474,845:244",
            "satoshi": 0
        }
    ],
    "ret": 0
}
```

The Hacash RPC API service adopts a standard http interface, and you can simply use it in a browser, with curl or in an application.

The following document will use the test interface provided by hacash.org as an example, visit [http://rpcapi.hacash.org/query?action=balances&address_list=1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS](http://rpcapi.hacash.org/query?action=balances&address_list=1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS) should return the same interface data content as above.

Example Two: [http://rpcapi.hacash.org/query?action=balances&address_list=1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS,1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9&unitmei=true](http://rpcapi.hacash.org/query?action=balances&address_list=1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS,1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9&unitmei=true) represents Query the balances of two addresses at the same time, and the returned balance unit is "mei".

### Interface format, general parameters

The standard HTTP interface request and response method is adopted, including 4 paths:

   1. /create generates data, GET method, such as creating an account, generating transfer transactions, etc.
   2. /submit submit data, POST method, such as submitting the transaction to the pool to be confirmed, etc.
   3. /query query data, GET method, such as query account balance, etc.
   4. /operate modify data, GET/POST method, such as modify system operating parameters, etc.

The parameters common to multiple interfaces are introduced as follows:

| Parameter name | Type | Default value | Example value | Function introduction |
| ---- | ---- | ---- | ---- | ---- |
| action | string |-| balances, diamond, block_intro, accounts ... | The type of data to be queried and generated, and the function of the interface is defined |
| unitmei | bool | false | true, false, 1, 0 | Whether to transfer or return the amount in units of "mei" of floating-point numbers, otherwise, use the Hacash standardized unit method. For example, use "mei" as the unit: "12.5086", and the standardized unit: "ㄜ125086:244" |
| unit | string  | - | mei,zhu,shuo,ai,miao | return the HAC amount use unit: mei, zhu, shuo, zi, miao |
| kind | menu |-| h, s, d, hs, hd, hsd | Filter the returned account and transaction information type. h: hacash, s: satoshi, d: diamond. Purpose: For example, when scanning a block, you only need to return the HAC transfer content and ignore the other two, just pass `kind=h`. |
| hexbody | bool | false | true, false, 1, 0 | `/submit` Whether to use the hex string form of Http Body when submitting data. The default format is native bytes. |


### Return value, public field

Respond to all requests in standard JSON format. The public fields are as follows:

```js

{
  "ret": 0, // indicates the return type, 0 is correct, >= 1 indicates an error occurred or the query does not exist
  "list": [...] // Some interfaces that return list data will be used
}

```

The following section will introduce the parameter passing and return value details of each specific interface.

---

## 1. /create create

#### 1.1 Create an account `GET: /create?action=accounts`

Create accounts in batches at random, and return a list of account information including private keys, public keys, and addresses. Passable parameters:

- number [int] indicates the number of accounts created in batches, the default is 1, the maximum is 200

Example interface: [http://rpcapi.hacash.org/create?action=accounts&number=5](http://rpcapi.hacash.org/create?action=accounts&number=5)

return value:

```js
{
    list: [
        {
            address: "1NLEYVmmUkhAH18WfCUDc5CHnbr7Bv5TaS", // account address
            prikey: "2e50243243abc2e41f3b2ae90029640e235d884a88cfb5ea3e4d0e9efbae6710", // private key
            pubkey: "03e22fc27a0d7ae325fa024875febd58266b8b6adbfb966116c9ba958ff5bad7e6" // public key
        },
        ...
    ],
    ret: 0
}
```

Special note: The system uses a random generation algorithm to create an account. The system does not retain or record the account private key created by this interface. Please make sure to back up and store the created private key, and take security protection.

#### 1.2 Create a transfer transaction `GET: /create?action=value_transfer_tx`

This interface is used to create HAC, one-way transfer of Bitcoin and block diamond transfer transactions. The basic parameters are as follows:

- unitmei [bool] Whether to use the unit "mei" floating point number form to analyze the passed amount parameter
- main_prikey [hex string] The private key hex string of the main address/handling fee address
- fee [string] The fee value to be given for the transaction, such as "0.0001" or "ㄜ1:244"
- timestamp [int] The timestamp of the transaction; it is optional to pass; if it is not passed, it is automatically set to the current timestamp
- transfer_kind [menu] (hacash, satoshi, diamond) The type of transaction to be created, HAC transfer, BTC transfer or block diamond transfer

[Note 1]: When only passing the same `timestamp` timestamp parameter and keeping other parameters always the same, each transaction created will have the same hash value and be regarded as the same transaction.

[Note two]: Since the Hacash system supports repeated signature fee bidding for the same transaction, only changing the `fee` field will not change the transaction's `hash` value, but only its `hash_with_fee` value.

[Note three]: The fee field of `fee` cannot be set too small, otherwise it will not be accepted by the entire system. The current minimum fee is 0.0001 (ie ㄜ 1:244). Please do not set the handling fee lower than this value.

##### 1.2.1 Create HAC ordinary transfer transaction

Pass the parameter `transfer_kind=hacash`, and add the following parameters:

 - amount [string] The transfer amount; the unit format depends on the `unitmei` parameter; for example, "0.1" or "ㄜ1:247".
 - to_address [string] Counter (receiving) account address

Call Interface Example: [http://rpcapi.hacash.org/create?action=value_transfer_tx&main_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=0.0001&unitmei=true&timestamp=1603284999&transfer_kind=hacash&amount=12.45&to_address=1NLEYVmmUkhAH18WfCUDc5CHnbr7Bv5TaS](http://rpcapi.hacash.org/create?action=value_transfer_tx&main_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=0.0001&unitmei=true&timestamp=1603284999&transfer_kind=hacash&amount=12.45&to_address=1NLEYVmmUkhAH18WfCUDc5CHnbr7Bv5TaS)


The return value is as follows:

```js
{
    // public parameters
    ret: 0,
    // The hash value of the transaction
    hash: "6066cef4fe51669aec5b5596375dba11dafaf2560c4bd8c0432ac4ea98ff3ad1",
    // The transaction contains the hash value of the fee
    hash_with_fee: "9cbc4821d0d921b429dbe4d6b67d6412aa5cae4b724f1e7dab9f870646cb1bb6",
    // The hex value of transaction body and content
    body: "02005f90300700e63c33a796b3032ce6b856f68fccf06608d9ed18f401010001000100e9fdd992667de1734f0ef758bafcd517179e6f1bf60204dd00010231745adae24044ff09c3541537160abb8d5d720275bbaeed0b3d035b1e8b263cb73b724218f13c09c16e7065212128cf0c037ebb9e588754eb073886486d950607d59bef462d2731e15b667c6ff1f0badd6259c6f58d5ca7a5f75856b8cae8e80000 ",
    // timestamp used in the transaction
    timestamp: 1603284999
}
```

In a production environment, please save the above return value in the database for reconciliation, or resubmit unconfirmed transactions when the blockchain network is delayed. The above content does not reveal your private key, but only the transaction data after the signature, please make sure to store it.

Create BTC transfer and block diamond transfer, the return value of calling the interface is the same as the above.

##### 1.2.2 Create transferred bitcoin ordinary transfer transaction

Pass the parameter `transfer_kind=satoshi`, and add the following parameters:

 - amount [int] The amount of bitcoins to be paid, in units of "satoshi" and "satoshi" (0.00000001 bitcoins); for example, transfer 10 bitcoins to transfer "1000000000", transfer 0.01 bitcoins to transfer "1000000"; system Bitcoin units below 1 satoshi are not supported.
 - to_address [string] Counter (receiving) account address

For example, to transfer a bit of an address token, the example interfaces such as:

[http://rpcapi.hacash.org/create?action=value_transfer_tx&main_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=0.0001&unitmei=true&timestamp=1603284999&transfer_kind=satoshi&amount=100000000&to_address=1NLEYVmmUkhAH18WfCUDc5CHnbr7Bv5TaS](http://rpcapi.hacash.org/create?action=value_transfer_tx&main_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=0.0001&unitmei=true&timestamp=1603284999&transfer_kind=satoshi&amount=100000000&to_address=1NLEYVmmUkhAH18WfCUDc5CHnbr7Bv5TaS)

##### 1.2.3 Create block diamond transfer transaction

Pass the parameter `transfer_kind=diamond`, and add the following parameters:

- diamonds [string] The literal value of diamonds separated by commas, such as "EVUNXZ,BVVTSI", one or more can be transferred, and up to 200 diamonds can be transferred in one batch
- diamond_owner_prikey [hex string] Optional, the account private key of the payment (payment of diamonds, diamond owner); if not passed, the default is `main_prikey`
- to_address [string] The account address of the other party (receiving diamonds)

Interface call Example:


[http://rpcapi.hacash.org/create?action=value_transfer_tx&main_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=0.0003&unitmei=true&timestamp=1603284999&transfer_kind=diamond&diamonds=EVUNXZ,BVVTSI&diamond_owner_prikey=EF797C8118F02DFB649607DD5D3F8C7623048C9C063D532CC95C5ED7A898A64F&to_address=1NLEYVmmUkhAH18WfCUDc5CHnbr7Bv5TaS](http://rpcapi.hacash.org/create?action=value_transfer_tx&main_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=0.0003&unitmei=true&timestamp=1603284999&transfer_kind=diamond&diamonds=EVUNXZ,BVVTSI&diamond_owner_prikey=EF797C8118F02DFB649607DD5D3F8C7623048C9C063D532CC95C5ED7A898A64F&to_address=1NLEYVmmUkhAH18WfCUDc5CHnbr7Bv5TaS)


---

## 2. /submit Submit

#### 2.1 Submit a transaction to the transaction pool `POST: /submit?action=transaction`

Submit a transaction to the memory pool of the entire network.

The url parameters are as follows:

- hexbody [bool] Pass the post body value in the form of hex string; otherwise pass it in the form of native bytes

The return value after the call is as follows

```js
{
    ret: 0 // ret = 0 means return success
}
```

Or return an error

```js
{
    ret: 1 // ret = 1 means submitting a transaction error
    // For example, if the balance is insufficient, the error message is as follows:
    errmsg: "address 1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9 balance ㄜ0:0 not enough, need ㄜ1,245:246."
}
```
Example of curl command line test call:

```shell script
curl "http://rpcapi.hacash.org/submit?action=transaction&hexbody=1" -X POST -d "02005f90300700e63c33a796b3032ce6b856f68fccf06608d9ed18f401010001000100e9fdd992667de1734f0ef758bafcd517179e6f1bf60204dd00010231745adae24044ff09c3541537160abb8d5d720275bbaeed0b3d035b1e8b263cb73b724218f13c09c16e7065212128cf0c037ebb9e588754eb073886486d950607d59bef462d2731e15b667c6ff1f0badd6259c6f58d5ca7a5f75856b8cae8e80000"
```

Handling fee bidding description 1: After the transaction is submitted, when you need to pay a higher handling fee as a diamond bid, or increase the handling fee to speed up the transaction confirmation, you can call the `/create` interface to create the same transfer transaction again, and only modify the amount The higher `fee` field (all other fields including the timestamp must be consistent, otherwise it will be two different transactions), and then resubmit the transaction, the entire network will discard the lower fee and pack this A transaction with a higher fee bid. All different fee bids are regarded as different states of a transaction, no matter how many bids are made, there will be only one package in the end.

Handling fee bidding explanation 2: When the transaction is still in the trading pool, you can call the `/operate` interface to modify the handling fee bidding more conveniently without resubmitting the entire transaction content. For interface description, see the content at the bottom of the document.

---

## 3. /query Query

#### 3.0 Summary

| Query | Arguments | Mandatory | Notes |
| ---- | ---- | ---- | ---- |
| balances | address_list, unitmei, kind,  |address_list| up to 200 comma, delmited addresses , kind : hsd, unitmei : boolean |
| diamond | name or number | name or number | singleton returns diamond information |
| last_block |  | | returns height |
| block_intro | height or hash | height or hash | returns block headers |
| scan_value_transfers | height, txhash, txposi, kind, unitmei| height or txhash| returns transaction for block height index txposi (default=0) |



#### 3.1 Query account balance `GET: /query?action=balances`

Check the HAC, BTC, and diamond balances of one or more accounts at once.

Pass parameters:

- address_list [string] : A comma-separated list of account addresses; up to 200 batch queries
- unitmei [bool] : Whether to return a floating-point number string in units of "mei"; otherwise, it returns a standard unit
- kind [menu] : Query the type of return; `kind=h` only returns HAC balance, `kind=hs` returns HAC, BTC balance, if you do not pass or pass `hsd`, all types of balances are returned

 Example Interface:

 [http://rpcapi.hacash.org/query?action=balances&unitmei=1&address_list=18Yt6UbnDKaXaBaMPnBdEHomRYVKwcGgyH,1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF](http://rpcapi.hacash.org/query?action=balances&unitmei=1&address_list=18Yt6UbnDKaXaBaMPnBdEHomRYVKwcGgyH,1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF)
 

 Example return value:

 ```js
{
    ret: 0, // general return value
    list: [
        {
            diamond: 350, // the number of diamonds
            hacash: "3101.0826", // HAC balance
            satoshi: 0 // BTC balance, unit "satoshi": satoshi
        },
        {
            diamond: 0,
            hacash: "3842.24693214",
            satoshi: 0
        }
    ]
}
```
Note: The returned balance list corresponds to the position of the passed account list.

#### 3.2 Query block diamond information `GET: /query?action=diamond`

Query detailed information such as the hash of a diamond, the height of the generated block, and the current address, etc.; you can query by diamond literal (name) or label (number).

Pass parameters:

- name [string] Diamond literal value, for example: "ZAKXMI"
- number [int] Diamond label, for example: "20001"
- unitmei [bool] : Whether to return a floating-point number string in units of "mei"; otherwise, it returns a standard unit

Example query interface 1: [http://rpcapi.hacash.org/query?action=diamond&number=20001](http://rpcapi.hacash.org/query?action=diamond&number=20001)

Example query interface 1: [http://rpcapi.hacash.org/query?action=diamond&name=ZAKXMI](http://rpcapi.hacash.org/query?action=diamond&name=ZAKXMI)

The above two interfaces return the same content:

```js
{
    ret: 0, // general return value
    number: 20001, // Diamond number
    name: "ZAKXMI", // Diamond name / face value
    approx_fee_offer: "ㄜ382:246", // The bidding fee for this diamond
    nonce: "00000000c9babb01", // Mining random numbers
    contain_block_height: 179610, // Height of excavated block
    contain_block_hash: "0000000010914f286cc035ee35ae0afc6294417adb775dddf25b3bd4aa22524b", // Hash of excavated block
    custom_message: "7d8367f6e46e9ffee311b9f3a38519d674b52407fd0aa287442715fe2f0c4db0", // Additional message
    prev_block_hash: "000000000ae89f8a1c93fbffe9baad198fda287f13e39c37294eb7a7b617bd70", // Last block hash containing diamond
    miner_address: "1KcXiRhMgGcvgxZGLBkLvogKLNKNXfKjEr", // The miner's address
    current_belong_address: "1KcXiRhMgGcvgxZGLBkLvogKLNKNXfKjEr" // Diamond's current belong address
}

```

We can see that the above two interface queries are essentially the same diamond.

#### 3.3 Query the latest valid block information `GET: /query?action=last_block`

Query the latest effective block information of the current blockchain.

params:

- info [bool] Whether to return details; otherwise, only height is returned

Request example: [http://rpcapi.hacash.org/query?action=last_block](http://rpcapi.hacash.org/query?action=last_block)

return data： 

```js
{
    ret: 0,
    height: 181719, // Currently, the block height that has been written into the effective blockchain
    
    // After passing the info field, more information is returned as follows:
    hash: "0000000003ba7ed589b5881ea1852276cc7feb9b6a18dc3d9dbdc3d110055bd6",
    mrkl_root: "5a28df311c7fb707914ff50394484e2079036284e38cbd1eaf811ab8c2ff34b1",
    prev_hash: "000000000c57ae62d74d7f05de67b949899f6115cac52441b109241d5da2edca",
    timestamp: 1603341936, // Block creation timestamp
    transaction_count: 0 // The block contains the number of transactions
}
```

#### 3.4 Query the specified block information `GET: /query?action=block_intro`

params:

- height [int] Optional, query block information by specifying height
- hash [string] Optional, query block information by block hash value
- unitmei [bool] Optional, whether to return the block reward and other amount fields with unit "Mei"
 
Example interface 1:  [http://rpcapi.hacash.org/query?action=block_intro&height=177255&unitmei=1](http://rpcapi.hacash.org/query?action=block_intro&height=177255&unitmei=1)

Example interface 2: [http://rpcapi.hacash.org/query?action=block_intro&hash=000000001e76b27f70f9a5e5b0f9b9824b416c210548ffad298577ed5378cdcd](http://rpcapi.hacash.org/query?action=block_intro&hash=000000001e76b27f70f9a5e5b0f9b9824b416c210548ffad298577ed5378cdcd)
 
return data：

```js
{
    ret: 0,
    coinbase: {
        address: "1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF", // Miner's address
        reward: "ㄜ1:248" // Reward for this block
    },
    height: 177255, // block height
    hash: "000000001e76b27f70f9a5e5b0f9b9824b416c210548ffad298577ed5378cdcd", // block hash
    mrkl_root: "d18df37a52c377115f053aca7822c65e5d154dac4ab6e062326dc770b5d1e5ad", // Merkel Root Hash
    nonce: 3327878209, // Mining random number
    difficulty: 3717887710, // Target difficulty value
    prev_hash: "000000001868742351b8ab196444d22c0db2951c522ebc5b9ef057ad70a6556c", // Previous block hash
    timestamp: 1601931702, // Block creation timestamp
    transaction_count: 5, // The block contains the number of transactions
    version: 1 // block version
}
```

#### 3.3 Scan every transaction for transfer information `GET: /query?action=scan_value_transfers`

Through the block height to traverse and scan each transaction, get the transfer content inside, to determine the exchange's functions such as charging and withdrawing money.

params:

- height [int] Block height to scan
- txposi [int] The transaction to be scanned is at the array index position in the block, starting from 0
- txhash [string] Optional, transaction hash. If the transaction hash is passed, you can directly query the transaction without passing the height and txposi values, which can be used to judge whether the transaction is confirmed or not
- unitmei [bool] Optional, whether to return floating-point string in units of "Mei"
- kind [menu] Query the type of return; `kind=h` returns only HAC transfer, `kind=hs` returns HAC and BTC transfer, and returns all types of transfer without transferring or passing `kind=hsd`
 
Examples 1: [http://rpcapi.hacash.org/query?action=scan_value_transfers&unitmei=1&height=180115&txposi=1&kind=hsd](http://rpcapi.hacash.org/query?action=scan_value_transfers&unitmei=1&height=180115&txposi=1&kind=hsd)
 
Examples 2: [http://rpcapi.hacash.org/query?action=scan_value_transfers&txhash=6d678040e5d3d8104de1ea627fde1973ab9d0e036a5fe20913dfdd6192dec266](http://rpcapi.hacash.org/query?action=scan_value_transfers&txhash=6d678040e5d3d8104de1ea627fde1973ab9d0e036a5fe20913dfdd6192dec266)
 
Examples return：


```js
{
    ret: 0,
    type: 2, // Transaction type
    hash: "6d678040e5d3d8104de1ea627fde1973ab9d0e036a5fe20913dfdd6192dec266", // Transaction hash
    // main_adderss
    address: "1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF", // Transaction fee address and main address
    feepay: "ㄜ4:244", // tx fee of user pay
    feegot: "ㄜ4:243", // tx fee of miner miner real received（the rest be burned）
    // Note: the tx fee paid by the user may be the same as or different from that obtained by the miner. But what miner get will never be more than what user payed.
    timestamp: 1602764644, // Transaction creation timestamp
    height: 103764, // Block height of Transaction confirm
    // Examples of scan transfer types are as follows:
    effective_actions: [
        {
            // mean main_adderss: 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF to to_Address: 16DL2tFV2PxLx4RsjKLFeFXm7sued4UbPv transfer 15.38 HAC, payment address 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF pay the fee: 0.0001 HAC
            ai: 0, // ai mean action_index, mean action at array index in this transaction is calculated from 0
            hacash: "ㄜ1538:246", // The hacash field indicates the number of HAC transfers
            to: "16DL2tFV2PxLx4RsjKLFeFXm7sued4UbPv"
        },
        {
            // mean from_address: 18o6aqFiUDurPqie6zrNwV2pmafWyziZZP to main_adderss: 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF transfer 50 HAC, Collection account 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF pay the fee.
            ai: 2,
            hacash: "ㄜ50:248", // The hacash field indicates the number of HAC transfers
            from: "18o6aqFiUDurPqie6zrNwV2pmafWyziZZP"
        },
        {
            // mean from_address: 1EM4j8xmxJosAi1N2RS2QL4VMe6x3agb7f to to_adderss: 1CYRSqhB7LgYXShHitxjBPaYcs4VxVWHyn pay 128 HAC ， main_address 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF Neither collection nor payment, only pay fee for this transfer.
            ai: 5,
            hacash: "ㄜ128:248", // The hacash field indicates the number of HAC transfers
            from: "1EM4j8xmxJosAi1N2RS2QL4VMe6x3agb7f",
            to: "1CYRSqhB7LgYXShHitxjBPaYcs4VxVWHyn"
        },
        {
            // Indicates the excavation / creation of the diamond. The miner's address is 18Yt6UbnDKaXaBaMPnBdEHomRYVKwcGgyH , Diamond name is SWBAVY , number is 20094.
            ai: 6,
            diamond: "SWBAVY",
            miner: "18Yt6UbnDKaXaBaMPnBdEHomRYVKwcGgyH",
            number: 20094
        },
        {
        	// mean main_adderss: 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF transfer diamond XMYVEM to to_address: 1CYRSqhB7LgYXShHitxjBPaYcs4VxVWHyn, main_address 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF pay the fee.
            ai: 8,
            diamonds: "XMYVEM",
            to: "1CYRSqhB7LgYXShHitxjBPaYcs4VxVWHyn"
        },
        {
            // bulk transfer of diamonds: that would main_adderss: 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF has three diamond WUZXYM and IZHTEW and IIUMWH have a one-time transfer to to_address: 1Bjj8rijMJSD6dRC6eSHFs6C3E1JzcTfyQ, be borne by the 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF fee in the deal; a bulk transfer up to 200 diamonds
            ai: 9,
            diamonds: "WUZXYM,IZHTEW,IIUMWH",
            to: "1Bjj8rijMJSD6dRC6eSHFs6C3E1JzcTfyQ"
        },
        {
            // Batch transfer of diamonds: it means that from_adderss: 1GRyscUQ2poZPKrR4wCxrkJqMrM8Wqnvm1 will not only charge the diamonds WUZXYM and IZHTEW for the transaction fee of 1GRyscUQ2poZPKrR4wCxrkJqMrM8Wqnvm1, and will transfer them to to_address: 19Co8BRxmh5uHBemvd8e9BbpyXJbWctnYY2HfEwnyds2, and the main account will not be charged for the transaction fee 1Ewnyds2.
            ai: 13,
            diamonds: "WUZXYM,IZHTEW",
            from: "1GRyscUQ2poZPKrR4wCxrkJqMrM8Wqnvm1",
            to: "19Co8BRxmh5uHBemvd8e9BbpyXJbRfeSiS"
        },
        {
        // Indicates that the diamond WSNZAX owned by from_adderss: 1JFo6YPy4niZPiBUBuKJQ3UQAe4LN8tCfw will be transferred to main_address: 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF, and the transaction fee of the payee 1DhctDS2HF will be paid by the transaction fee of 3mctDS2HFWnhjcwwnE3dWgtiqe2mctDS2HF
            ai: 14,
            diamonds: "WSNZAX",
            form: "1JFo6YPy4niZPiBUBuKJQ3UQAe4LN8tCfw"
        },
        {
        // One-way transfer of bitcoins starts the transaction, indicating how many bitcoins have been transferred to the Hacash mainnet
            ai: 17,
            btctrsno: 3, // transfer serial number of Bitcoin to Hacash
            owner: "17XTDDHWjbUoKZmgFmdTg1m9UHYLuX4JVe", // Bitcoin owner address
            satoshi: 200000000 // The amount of bitcoins transferred, in "satoshi" satoshi, this value represents 2 bitcoins
        },
        {
        // Bitcoin transfer: 0.1 Bitcoin from main_address: 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF to to_address: 1KfVAAgSkfDVfr3WLkcsqBcptKwm2ffYhB, the transaction fee of this transaction is borne by the main address of the payer 1DYY4ZRsWnhjcw
            ai: 18,
            satoshi: 10000000, // The amount of bitcoins transferred, in "satoshi" satoshi, this value represents 0.1 bitcoins
            to: "1KfVAAgSkfDVfr3WLkcsqBcptKwm2ffYhB" // Bitcoin payment address
        },
        {
        // Bitcoin transfer: From form_address: 1PXosXwBwcYTXS7wHq7agMJgYpHqdMYoG5 to main_address: 1DYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF Bitcoin 0.05, the transaction fee is paid by the payee's address 1DYYjcwsWn 3dYY4ZRsWnhjcwwnE3dWgtiqe2mctDS2HF
            ai: 19,
            satoshi: 5000000, // The amount of bitcoins transferred, unit: "satoshi" satoshi, this value represents 0.05 bitcoins
            form: "1PXosXwBwcYTXS7wHq7agMJgYpHqdMYoG5" // Bitcoin payment address
        },
        {
        // Bitcoin transfer: from form_address: 1PXosXwBwcYTXS7wHq7agMJgYpHqdMYoG5 to to_address: 1EM4j8xmxJosAi1N2RS2QL4VMe6x3agb7f Bitcoin is only 1.2, the main address is 1DYY4ZRsWe3dwcDSWnhjWcWnhjWc, the transaction fee is not charged, and the transaction fee is not charged.
            ai: 20,
            satoshi: 120000000, // The amount of bitcoins transferred, unit: "satoshi" satoshi, this value represents 1.2 bitcoins
            form: "1PXosXwBwcYTXS7wHq7agMJgYpHqdMYoG5", // Bitcoin payment address
            to: "1EM4j8xmxJosAi1N2RS2QL4VMe6x3agb7f" // Bitcoin payment address
        }
    ]
}
```

Use the `scan_value_transfers` interface note:

- Regardless of transferring HAC, BTC or block diamonds, only HAC can be used to pay the handling fee
- The unit of BTC transfer is "satoshi", 1 BTC = 100000000 satoshi
- Judge the transfer type through the fields of `hacash`, `satoshi` and `diamonds`, as well as `miner` and `owner`
- The `ai` field represents `action_index`, which is the array index position of the transfer in the transaction, not the type
- Judge the direction of fund transfer by checking the two address fields of `from` and `to`
- The Hacash system supports the payer, receiver or any unrelated third party to pay transaction confirmation fees when transferring funds
- A transaction can include transfers of multiple types, amounts, and multi-payments. A transaction is a comprehensive payment contract
- It is possible to add other types of transfer types in the future


#### 3.6 Scan each transaction to obtain a brief transfer operation `GET: /query ? action=scan_coin_transfers`

The `scan_value_transfers` interface returns richer and more structured content. If you only need to obtain transfer actions for HAC, HACD, and BTC for simple needs such as exchange recharge, you can use `scan_coin_transfers` interface makes the content more concise.

Pass parameters:

- height [int] Block height to scan
- txposi [int] The transaction to be scanned is at the array index position in the block, starting from 0
- txhash [string] Optional, transaction hash. If the transaction hash is passed, you can directly query the transaction without passing the height and txposi values, which can be used to judge whether the transaction is confirmed or not
- unitmei [bool] Optional, whether to return floating-point string in units of "Mei"
- kind [menu] Query the type of return; `kind=h` returns only HAC transfer, `kind=hs` returns HAC and BTC transfer, and returns all types of transfer without transferring or passing `kind=hsd`
- from [string] The from address to filter will only display the incoming address after passing, ignoring other from addresses
- to [string] The to address to filter will only display the incoming address after passing, ignoring other to addresses

Example API 1：[http://rpcapi.hacash.org/query?action=scan_coin_transfers&unitmei=1&height=180115&txposi=1&kind=hsd](http://rpcapi.hacash.org/query?action=scan_coin_transfers&unitmei=1&height=180115&txposi=1&kind=hsd)

Example API 2：[http://rpcapi.hacash.org/query?action=scan_coin_transfers&txhash=6d678040e5d3d8104de1ea627fde1973ab9d0e036a5fe20913dfdd6192dec266](http://rpcapi.hacash.org/query?action=scan_coin_transfers&txhash=6d678040e5d3d8104de1ea627fde1973ab9d0e036a5fe20913dfdd6192dec266)

Example Return：

```js
{
    hash: "43e98177bc426a2f15c15fe3e8968ece1b2e0829eab7cacb8715c89082f48aef",
    height: 490194,
    ret: 0,
    timestamp: 1698278005,
    transfers: [
        {
            // By default, both from and to addresses will be displayed, making it easier to query
            from: "13RnDii79ypWayV8XkrBFFci29cHtzmq3Z",
            to: "1EcrtFAUmVeLnGeaEcMDoPZH7ZPysks1H2",
            hacash: "0.9370996"
        },
        {
            from: "13RnDii79ypWayV8XkrBFFci29cHtzmq3Z",
            to: "1EcrtFAUmVeLnGeaEcMDoPZH7ZPysks1H2",
            sotoshi: "5000000"
        },
        {
            from: "13RnDii79ypWayV8XkrBFFci29cHtzmq3Z",
            to: "1EcrtFAUmVeLnGeaEcMDoPZH7ZPysks1H2",
            diamond: "WUZXYM"
        },
        {
            from: "13RnDii79ypWayV8XkrBFFci29cHtzmq3Z",
            to: "1EcrtFAUmVeLnGeaEcMDoPZH7ZPysks1H2",
            diamond: "WUZXYM,IZHTEW,IIUMWH" // Batch transfer of diamonds
        }
    ],
    type: 2
}

```


#### 3.7 Query total supply `GET: /query ? action=total_supply`

 examples link：[http://rpcapi.hacash.org/query?action=total_supply](http://rpcapi.hacash.org/query?action=total_supply)
 
 examples return data：
 
```js
{
    ret: 0,
    minted_diamond: 27538, // The number of diamonds that have been minted successfully
    transferred_bitcoin: 0, // Number of bitcoins successfully transferred to hacash main network, unit: BTC
    block_reward: 240213, // Block reward HAC accumulation
    channel_interest: 151.6902713463587, // Channel interest HAC accumulation
    btcmove_subsidy: 0, // BTC transfer and additional issuance of HAC
    burning_fee: 0, // tx fee by burning
    current_circulation: 240364.69027134636, // Current circulation supply
    located_in_channel: 0, // Real time statistics of HAc located in channel chain

}
```

---

## 4. /operate Modification, operation

#### 4.1 Increase transaction fee for confirmation `GET: /operate?action=raise_tx_fee`

When a transaction exists in the transaction pool and has not been confirmed, it can be re-signed to increase (but not reduce) the transaction fee, so as to speed up transaction confirmation or diamond bidding.

Pass parameters:

- txhash [string] The hash value of the transaction to increase the fee
- fee [string] The target fee to be modified
- unitmei [bool] Optional, whether to use the unit "piece" as the unit passed in the `fee` field
- fee_prikey [string] The private key of the fee address

Example Access Interface: 

[http://rpcapi.hacash.org/operate?action=raise_tx_fee&fee_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=ㄜ32:247&txhash=ad26a35116664176426f3c08adad147577b9a85999cb89d465becf6a27002c04](http://rpcapi.hacash.org/operate?action=raise_tx_fee&fee_prikey=8D969EEF6ECAD3C29A3A629280E686CF0C3F5D5A86AFF3CA12020C923ADC6C92&fee=ㄜ32:247&txhash=ad26a35116664176426f3c08adad147577b9a85999cb89d465becf6a27002c04)``

Example of interface return:

```js
{
    ret: 0,
    status: "success" // Indicates that the fee increase is successful
}
```

Example of failed return:

```js
{
    ret: 1,
    errmsg: "Tx fee address password error: need 18Yt6UbnDKaXaBaMPnBdEHomRYVKwcGgyH but got 1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9" // Reason for failure: Inconsistent fee address
}

```   
