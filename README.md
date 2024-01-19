# Paper
Whitepaper & Documents & Hacash improvement proposals & theoretical articles



- [Whitepaper](https://github.com/hacash/paper/blob/master/whitepaper.pdf)
- [HIP Table (Hacash improvement proposals list)](https://github.com/hacash/paper/blob/master/HIP/HIP-table.md)



### Software download & release log

Release: [Software Release](https://github.com/hacash/node/releases)

### Compilation build instructions 

Compilation build instructions: [build_compilation_en.md](https://github.com/hacash/paper/blob/master/build/build_compilation.md)

---

### RPC API Doc 

[rpc_api_doc.en.md](https://github.com/hacash/paper/blob/master/service/rpc_api_doc.md) 

---

### Miner service Doc

[miner_service_api.en.md](https://github.com/hacash/paper/blob/master/service/miner_service_api.md)



---

### X16RS algorithm design explanation

[x16rs_algorithm_description.en.md](https://github.com/hacash/paper/blob/master/tech/x16rs_algorithm_description.md)


---

### HAC & HACD mining fairness description

[HAC_HACD_mining_fairness_description.en.md](https://github.com/hacash/paper/blob/master/tech/HAC_HACD_mining_fairness_description.md)


---

### Project code engineering architecture

The architecture of Hacash full node code is divided into 7 levels from bottom to top:

X16RS -> Core -> Chain -> Mint -> Node -> Service -> Miner

Each layer of the architecture has independent functions and responsibilities for the upper layer to call, and the implementation of the lower layer to the upper layer is unknown. The responsibilities of each layer are roughly as follows:

1. [X16RS] Basic algorithm - including HAC mining, block diamond mining, GPU version algorithm, etc.
2. [Core] Core - block structure definition, interface definition, data serialization and deserialization, storage object, field format, genesis block definition, etc.
3. [Chain] Chain - underlying database, block and transaction storage, blockchain state storage, logs, etc.
4. [Mint] Mint - block mining difficulty adjustment algorithm, coinbase definition, block construction, transaction execution and status update, etc.
5. [Node] Node - P2P underlying module, Backend blockchain synchronization terminal, point-to-point network message definition and processing, etc.
6. [Service] Service - RPC API interface service, block and transaction and account data query, other services, etc.
7. [Miner] Miner - block construction and mining, diamond mining, transaction memory pool, mining pool server, mining pool worker, etc.


```cgo
// compile NOTE: set go module env config
GO111MODULE="auto"
```
