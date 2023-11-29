# Hacash mining specifications, data and service interface documents 

This document contains the  operation mode and interface specifications of a series of data and  services related to Hacash mining, which is used to develop third-party  commercial mining pools, private mining clients, or mining pool  statistical services, and other mining-related businesses. 

## 

## 1 Architecture 

We use the "Relay Nodes" method to provide mining-related data interface services, as shown below: 

[![miner relay service](https://github.com/hacash/service/raw/master/doc/miner_relay_service.png)](https://github.com/hacash/service/blob/master/doc/miner_relay_service.png)

The relay node is not only the server that connects several mining clients, but also the server of all mining-related data interfaces. Several relay nodes can be mounted  under a Hacash full node (based on TCP connection, the number of  connections depends on the server performance and bandwidth), and the  relay nodes can also be connected in a tree-like series (that is, the  relay node is used as the server, and then connected below Mount several relay nodes, and can be connected in multiple layers), so that  theoretically countless mining client machines can be mounted under a  full node. 

In addition to connecting to  the relay node, the mining client (miner machine) can also directly  connect to the full node when necessary. In addition to providing  real-time mining-related status and data, and calculating and generating data preparation materials required for mining, the relay service node  can also count and save the computing power data of the connected  miners, and provide a query interface for computing power statistics.  Provide statistical data for the calculation of the overall computing  power of the higher-level mining pool and the calculation of reward  distribution. 

## 

## 2 Deployment and operation 

Please in the [software_release_log.md ](https://github.com/hacash/miner/blob/master/doc/software_release_log.md)download the full node software, relay service node software and miner client  software address, and run them according to the following configuration. 

The first step: first need to run a Hacash full node, related configuration files `hacash.config.ini` as follows: 

```
data_dir  = ./hacash_mainnet_data

 [p2p] 
listen_port  = 3337
 boot_nodes  = 182.92.163.225:3337,47.244.26.14:3337

 [miner] 
enable  = true
 rewards  = 1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS

 [minerserver] 
enable  = true
 listen_port  = 3350 
```

During the above arrangement, `[miner]`Must be turned on, which means that the full node turns on mining related functions. and `[minerserver]`Part must also be turned on at the same time, which means that the mining  server service is turned on, and the service listening TCP port is `3350`, The relay node needs to connect to this port to synchronize the latest block information. 

The command line running example is as follows: 

```
./hacash_node_2021_05_15_01 hacash.config.ini 
```

Step 2: Run the relay node and connect to the full node, related configuration files `hacash_relay_service.config.ini` as follows: 

```
server_connect  = 127.0.0.1:3350
 server_listen_port  = 19991

 http_api_listen_port  = 8080

 accept_hashrate  = true
 report_hashrate  = true

 [store] 
enable  = true
 data_dir  = ./hacash_relay_service_data
 save_mining_block_stuff  = true
 save_mining_hash  = true
 save_mining_nonce  = true 
```

The above configuration, where `server_connect`Indicates the full node or superior relay node to be connected to. `server_listen_port`Indicates the port monitored by this relay service for the connection of lower-level relay nodes. `http_api_listen_port`Represents the listening port of the http api service. `accept_hashrate`Indicates whether to accept the computing power statistics reported by the lower-level relay node, `report_hashrate`Indicates whether to submit computing power statistics to the superior relay node or all nodes. 

`[store]`The configuration part is related to saving calculation power statistics and mining logs. 

The command line running example is as follows: 

```
./hacash_miner_relay_service_2021_05_12_01 hacash_relay_service.config.ini 
```

Under normal circumstances, you can see the output: 

```
Load ini config file: "./hacash_relay_service.config.ini" at time:07/15 17:17:27
[Miner Relay Service] Start server and listen on port 19991.
[Miner Relay Service] Http api listen on port: 8080
connecting server <127.0.0.1:3350>... success.
note: pool is not accept PoW power statistics.
receive new block <257282> mining stuff forward to [0] clients at time 07/15 17:17:28.
```

After the service is running correctly, we visit `http://127.0.0.1:8080/`Will treat the following information 

```
{ 
  ret :  0 , 
  service :  "hacash miner relay service" 
} 
```

Indicates that the HTTP interface service is running normally. 

Step 3: Run the mining client and related configuration files `minerworker.config.ini` as follows: 

```
pool  = 127.0.0.1:19991

 rewards  = 1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS

 supervene  = 1  
```

Configuration `pool`It is the address of the relay node or full node to be connected to. `rewards`It is the statistical address for collecting mining rewards. `supervene`For the number of concurrent mining, the setting value is related to the number of CPU cores. 

The command line running example is as follows: 

```
./hacash_miner_worker_2021_05_24_01 minerworker.config.ini 
```

After running, the mining client will connect to the relay service node `127.0.0.1:19991`Start mining on the above, under normal circumstances, the following information will be output: 

```
[Start] connect: 127.0.0.1:19991, reward: 1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS. 
startup CPU device of [1] supervene.
connecting miner server...connected successfully.
do mining height:‹259297›, difficulty: 00000000054e5b1...
```

Indicates successful connection and mining 

## 

## 3 HTTP interface service 

#### 

#### 3.1 [GET] /query?action=pending_block Get the block information currently being mined 

Sample interface access: `http://127.0.0.1:8080/query?action=pending_block&only_height=0&unitmei=0`

Optional parameters: 

- [only_height]  `bool`Whether to return only the block height, used to determine whether the height of the newly mined block has changed 
- [unitmei] `bool`Whether to use floating point units `枚`Returns the number of HAC in the form of 

Examples of return values: 

```
{ 
    block :  { 
        height :  257301 
    } , 
    ret :  0 
} 

// 或者 

{ 
    block :  { 
        difficulty :  3670274902 , 
        height :  257301 , 
        prevhash :  "00000000006cddd0c5837d077509714aa8070588599ce35ad41188c14a911ec9" , 
        timestamp :  1626342425 , 
        transaction_count :  1 , 
        version :  1 , 
        witness_stage :  0 
    } , 
    coinbase :  { 
        address :  "1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS" , 
        body_version :  1 , 
        message :  "" , 
        message_hex :  "20202020202020202020202020202020" , 
        reward :  "ㄜ2:248" , 
        witness_count :  0 , 
        witness_sigs :  [  ] , 
        witnesses :  [  ] 
    } , 
    mrkl_miner_related_hash_list :  [  ] , 
    ret :  0 
} 
```

This interface will return all block information related to mining (excluding transaction lists). In  theory, third-party developers can “assemble” block header data  according to the block data format, and according to Merkel related hash (ie `mrkl_miner_related_hash_list`Parameters) to get the basic data to be mined. But this requires a deep  understanding of Hacash's block number format details, which is too  complicated for third-party developers. So we next provide another very  simple interface to get the basic data to be mined. 

#### 

#### 3.2 [GET] /query?action=mining_stuff Get mining module data 

Sample interface access: `http://127.0.0.1:8080/query?action=mining_stuff`

Examples of return values: 

```
{ 
    ret :  0 , 
    height :  257301 , 
    target_difficulty_hash :  "00000000061f9ab0000000000000000000000000000000000000000000000000" , 
    coinbase_nonce :  "6d4519e65ec75d191d741343d3a46fcf8f75081aacddd01d2be25f0febb9d760" , 
    stuff :  "01000003ed150060f0041900000000006cddd0c5837d077509714aa8070588599ce35ad41188c14a911ec9dbe4be5a0ce2729659e5dbba7d6dea54a9e4b62dffd811a1cfad66ae33eac0390000000100000000dac3f3560000" , 
    head_nonce_start :  79 , 
    head_nonce_len :  4 
} 
```

This interface returns the  basic data to be mined in the most simple and backward compatible  format, and the third party does not need to understand the details of  the block format. The specific return value is described as follows: 

- [height]  `int`The height of the block to be mined 
- [target_difficulty_hash] Difficulty target hash value, used to judge whether mining is successful 
- [coinbase_nonce] `hex string`The random nonce value returned in HEX format. This value will be randomly  generated each time the interface is accessed. It is a fixed-length  32-bit hash, which is different each time. This value needs to be  temporarily saved in the mining client, so that it can be sent back when reporting the mining results to the server. The specific purpose will  be explained below 
- [stuff]  `hex string`The basic block mining data returned in HEX format is used to do `x16rs`Input data for algorithm mining 
- [head_nonce_start]  `int`The starting position of the block header nonce value in the basic mining data (the stuff return value) 
- [head_nonce_len]  `int`The length of the nonce value of the block header 

Like other PoW-based mining  projects, Hacash also repeatedly tries a certain hash operation  (constantly changing the input value) until it finds a hash that meets  the difficulty target.  Hacash's block mining hash function uses  original `X16RS`Algorithm, to understand the details of this algorithm, please refer source code [https://github.com/hacash/x16rs/blob/master/x16rs.go#L72 ](https://github.com/hacash/x16rs/blob/master/x16rs.go#L72)to the two related functions in the : 

```
func  HashX16RS ( loopnum  int ,  data  [] byte ) [] byte  {
     // ... 
}

 func  CalculateBlockHash ( blockHeight  uint64 ,  stuff  [] byte ) [] byte  {
     // ... 
} 
```

in `CalculateBlockHash(uint64, []byte)`The two parameters of the function are respectively returned by the above interface `height` and `stuff`The value of this function returns a 32-bit hash result, that is, the block hash, which is used to determine whether the mining is successful. But  if the same height and stuff values are passed each time, the hash  result returned by the CalculateBlockHash function remains unchanged. 

The process of mining is to try a different one each time `stuff`Value as `CalculateBlockHash`The parameters of the function, the process of performing operations  multiple times to obtain different results, and judging whether the hash result meets the difficulty requirement (that is, the  target_difficulty_hash return value is a hash that is "smaller" than the target difficulty hash definition). 

but `stuff`The value of is not completely random, it contains the structured data of the block header, so it cannot be passed randomly `stuff`Value, and must be in the above interface `stuff`Based on the returned data, modify according to the rules to get the legal `stuff`Value. Legal modification requires the help of `head_nonce_start` as well as `head_nonce_len`Two parameters, that is, only the specified part of the stuff data defined  by these two values can be modified randomly, we call it `head_nonce`, And other parts cannot be modified, otherwise the block data will be  destroyed and mining will be invalid. Since backward compatibility  issues such as block format version upgrades must be considered, so `stuff`The part of the value that can be randomly replaced may also be upgraded in the future, so `head_nonce_start` with `head_nonce_len`The value of is not fixed, which means `stuff`The parts that can be randomly replaced legally, including position and length, are also subject to change. 

For example, the interface returned by the above `stuff`The value is a buffer with a length of 89, `head_nonce_start`Returns 79 and `head_nonce_len`Returning 4 means that the index of the stuff data buffer array (the subscript  starts from 0) The last 4 digits starting from the 79 bit can be  replaced at random `head_nonce`The value, specifically the data enclosed in parentheses in the following  data (ie 00000001 four-bit byte) is the part that can be replaced at  random. 

01000003ed380060f035f000000000011c057cabe71255adb713ffda047e429e602054154416432299e3b089ba5abbbbc8eca9c38d5bcdada7fdf8c81027b61ddfd11adb27be19520ecf63( `00000001`)00000000dac3f3560000 

A total of four digits can be randomly replaced, so there are a total of `255^4 = 4228250625`Approximately 4.2 billion hash operation attempts. If 4.2 billion `head_nonce`After all the attempts are completed but the hash result of the compound  target difficulty is still not found, you need to call this interface  again (ie /query?action=pending_block) to obtain a new one `stuff`Value and a new 32-bit `coinbase_nonce`Value. In other words, there are actually two nonce values inside the Hacash block that can be randomly attempted to replace: `coinbase_nonce` with `head_nonce`. And once `coinbase_nonce`Value changes, then `head_nonce`There will be another 42 attempts. However, in order to encapsulate technical details and maintain the simplicity of third-party mining, from `coinbase_nonce`Value combined with block header data to generate effective `stuff`This process is hidden and is automatically and randomly generated every time the interface is called `coinbase_nonce`For everyone to use directly. 

So how to judge `CalculateBlockHash`Does the hash result of the mining function meet the difficulty target (that is, successful mining)? Let's look at another located at [https://github.com/hacash/mint/blob/master/difficulty/check.go#L14 ](https://github.com/hacash/mint/blob/master/difficulty/check.go#L14)function `CheckHashDifficultySatisfy`： 

```
func  CheckHashDifficultySatisfy ( result_hash ,  target_diffculty_hash  [] byte )  bool  {
	 if  len ( result_hash )  !=  32  ||  len ( target_diffculty_hash )  !=  32  {
		 panic ( "CheckHashDifficultySatisfy hx1, hx2 size must be 32." )
	}
	 for  k  :=  0 ;  k  <  32 ;  k ++  {
		 if  result_hash [ k ]  <  target_diffculty_hash [ k ] {
			 return  true 
		}  else  if  result_hash [ k ]  >  target_diffculty_hash [ k ] {
			 return  false 
		}
	}
	 return  true 
} 
```

This function is very simple, that is, compare the results of hash calculation attempts `result_hash`Hash value with target difficulty `target_diffculty_hash`When the resulting hash is "smaller" than the target difficulty (that is,  the number of leading zeros in the hash value is more), it means that  the mining is successful. 

#### 

#### 3.3 [GET] /submit?action=mining_result Submit mining results (mining success or calculation power statistics) 

Sample interface access: `http://127.0.0.1:8080/submit?action=mining_result&mint_success=0&reward_address=1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS&block_height=257370&head_nonce=f6f6f6f6&coinbase_nonce=6d4519e65ec75d191d741343d3a46fcf8f75081aacddd01d2be25f0febb9d760`

Parameter Description: 

- [mint_success]  `bool`Locally judge whether the mining is successful (the remote server will make another judgment) 
- [reward_address]  `string`Income account for mining reward statistics 
- [block_height]  `int`The block height of this mining 
- [head_nonce]  `hex string`Random number result of mining 
- [coinbase_nonce] `hex string`The coinbase_nonce value obtained from the interface and returned as it is 

Return result: 

```
{ 
    ret :  0 , 
    stat :  "ok" 
} 
```

On the previous interface `/query?action=mining_stuff`Acquired within `coinbase_nonce`The value needs to be returned intact in this interface. The server will  use this parameter to assemble the block header data and verify the hash operation result. 

When we judge that a block is successfully mined, we call this interface and submit `head_nonce`And related data, the relay node will be submitted to the full node layer  by layer, and after verification, the successfully mined block will be  broadcast to the entire network. All other miners will receive the new  block and automatically start mining the next block. Piece. 

Similarly, when others  successfully mine a block, a new block notification will arrive at each  miner, and all miners will automatically call `/query?action=mining_stuff`The interface obtains the next block data to be mined and starts a new round of mining. 

But the HTTP interface is a stateless protocol, you can poll regularly `/query?action=pending_block&only_height=1`Determine whether there is a new area coming soon, so as to determine whether to  switch to the next mining block. Of course, if your mining machine  performance is good enough, you can try 4.2 billion times in a few  seconds or even a second. `head_nonce`Value space, then there is no need to poll and monitor new blocks, `/query?action=mining_stuff`The interface will automatically return the new block data to be mined. 

#### 

#### 3.4 [GET] /uery?action=mining_result Query computing power statistics 

The log of all hashrate  statistics can be queried through the global auto-increment sequence  number (similar to the auto-increment primary key of a database)  composed of idx_start and idx_limit.  The only_worth parameter marks  whether to ignore other statistical information, and only returns  result_hash_worth, which is the hash rate statistical value of the  computing power.  result_hash_worth is the main indicator that the  mining pool determines the proportion of rewards allocated according to  the size of the computing power. 

Sample interface access 1: `http://127.0.0.1:8080/query?action=mining_result&idx_start=1&idx_limit=10&only_worth=0`

Return value example 1: 

```
{ 
    list :  [ 
    { 
        block_height :  259297 , 
        coinbase_nonce :  "e5fd87815870054a7c4f210fa7f8ed80fd4f6793ecfdacbbcf26dbcff4bafad1" , 
        head_nonce :  "57f03300" , 
        mint_success :  0 , 
        result_hash :  "00000001218476129eaaf2e665ac11195026786bfd3f68630ae3c654042ef62c" , 
        result_hash_worth :  "4275993344" ,  // 算力哈希率 
        reward_address :  "1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS" 
    } , 
    { 
        block_height :  259298 , 
        coinbase_nonce :  "4cbc0c77bbf17b992285a9dcabbf199dbd7219106254fa72f8960a1341f7b3d4" , 
        head_nonce :  "99190d00" , 
        mint_success :  0 , 
        result_hash :  "0000031336414d6b1db663c289f061d68c0e491b6c01fe9b30ccd16dc2c4d204" , 
        result_hash_worth :  "16575689" , 
        reward_address :  "1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS" 
    } , 
    ...
     ] 
} 
```

In addition to querying  computing power statistics by log serial number, you can also query more precise and detailed statistics through block height and reward  address. 

Sample interface access 2: `http://127.0.0.1:8080/query?action=mining_result&reward_address=1AVRuFXNFi3rdMrPH4hdqSgFrEBnWisWaS&block_height_start=259297&block_height_limit=10&only_worth=1`

Return value example 2: 

```
{ 
    list : [ 
    { 
        block_height : 259297 , 
        result_hash_worth : "4275993344"  // If the only_worth parameter is passed, only the hashrate statistics will be returned 
    } , 
    { 
        block_height : 259298 , 
        result_hash_worth : "16575689" 
    } , 
     ... 
     ] 
} 
```

This interface is used to  query the mining result log and computing power statistical record  reported by the mining client. It is the necessary statistical data for  independent development of third-party mining pools. Of course, this is  not necessary. If you have the corresponding development capabilities  and understand the underlying technology of Hacash, third parties can  develop their own computing power statistics services. 

#### 

#### 3.5 [GET] /uery?action=mining_result Query computing power statistics 

Sample interface access: `http://127.0.0.1:8080/query?action=historical_block&height=259298`

Examples of return values: 

```
{ 
    block :  { 
        difficulty :  3668560738 , 
        height :  259298 , 
        prevhash :  "00000000011e293f92e9d93ea9ab2dce6a012fb1e8f467e9ab08511813fb162e" , 
        timestamp :  1626945557 , 
        transaction_count :  1 , 
        version :  1 , 
        witness_stage :  0 
    } , 
    coinbase :  { 
        address :  "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9" , 
        body_version :  1 , 
        message :  "unknown" , 
        message_hex :  "756e6b6e6f776e202020202020202020" , 
        reward :  "ㄜ2:248" , 
        witness_count :  0 , 
        witness_sigs :  [  ] , 
        witnesses :  [  ] 
    } , 
    mrkl_miner_related_hash_list :  [  ] , 
    ret :  0 
} 
```

This interface is to query the historical mining block information of this relay node (regardless of  whether it is successfully mined) for possible computing power  verification. 

<details class="details-reset details-overlay details-overlay-dark" id="jumpto-line-details-dialog">
    <summary data-hotkey="l" aria-label="Jump to line" role="button"></summary>
    
  </details>

- © 2021 GitHub, Inc. 
- [Terms ](https://docs.github.com/en/github/site-policy/github-terms-of-service)
- [Privacy ](https://docs.github.com/en/github/site-policy/github-privacy-statement)
- [Security ](https://github.com/security)
- [Status ](https://www.githubstatus.com/)
- [Docs ](https://docs.github.com)

[            ](https://github.com)

[  ](https://github.com)

- [Contact GitHub ](https://support.github.com)
- [Pricing ](https://github.com/pricing)
- [API ](https://docs.github.com)
- [Training ](https://services.github.com)
- [Blog ](https://github.blog)
- [About ](https://github.com/about)

​    
