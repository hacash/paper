Hacash Mining Technology Development Description
===

> This document is intended for developers of third-party mining tools, platforms or services, and is a development guide and mining algorithm description for technical developers who want to access Hacash mining related matters such as professional mining machines (system integration or software and hardware integration, professional hardware, etc.), mining pools, computing power data platform services, etc.
> If you need to know the basics of Hacash mining output, please refer to the [HAC and HACD Mining Fairness Notes↗](https://github.com/hacash/doc-chinese/blob/main/tech/HAC_HACD_mining_fairness_description.md)

Hacash's block mining and diamond (HACD) mining algorithms are based on the original [X16RS↗](https://github.com/hacash/x16rs/), which was [open-sourced↗](https://github.com/hacash/x16rs/) with the full node code at the launch of the Hacash mainnet

Hacash mining is divided into two separate parts, blocks and diamonds, which are divided into multiple links, and this article will describe the details of the mining algorithm and related precautions in detail. 

## X16RS

The underlying algorithm of X16RS is partly the same as the X16R algorithm used by Ravencoin, which is also a combination of 16 SHA3 algorithms. X16RS (Random Scalable) is an upgraded version of the X16R (Random) algorithm, and the improvement of X16RS is that the algorithm is randomly selected at each step, replace X16R hash determined once per block. thus greatly increasing the difficulty of FPGA or ASIC design. The 16 hashing algorithms used are:

```C
enum AlgoX16 {
    BLAKE = 0,
    BMW,
    GROESTL,
    JH,
    KECCAK,
    SKEIN,
    LUFFA,
    CUBEHASH,
    SHAVITE,
    SIMD,
    ECHO,
    HAMSI,
    FUGUE,
    SHABAL,
    WHIRLPOOL,
    SHA512
};
```

- [Related algorithms link↗](https://github.com/hacash/x16rs/blob/7b2afef3bafb0a6d7f7301b04230dfa00ed52508/x16rs.c#L56)

The key part of X16RS randomness is to take the last 4 bits of the previous round of hash results (32 bits) as a uint32 value, take the remainder of 16, and obtain an integer in the range of 0~15, which is used as the selection subscript of the list of 16 SHA3 algorithms, and the corresponding algorithm is taken out to perform the next hash calculation. This means that the hashing algorithm selected for each next step must be based on the calculation result of the previous hash, and the calculation chain cannot be predicted in advance. If the algorithm is executed 16 times, there are 16^16 approximately 1.84467440737096e+19 computation paths, which is the core of the massive randomness that leads to the X16RS algorithm:

```C
void hash_x16rs_choice_step(hash_t* stephash){

    uint8_t algo = stephash->h4[7] % 16;

    switch (algo) {
        case BLAKE:     hash_x16rs_func_0 ( stephash ); break;
        case BMW:       hash_x16rs_func_1 ( stephash ); break;
        case GROESTL:   hash_x16rs_func_2 ( stephash ); break;
        case JH:        hash_x16rs_func_3 ( stephash ); break;
        case KECCAK:    hash_x16rs_func_4 ( stephash ); break;
        case SKEIN:     hash_x16rs_func_5 ( stephash ); break;
        case LUFFA:     hash_x16rs_func_6 ( stephash ); break;
        case CUBEHASH:  hash_x16rs_func_7 ( stephash ); break;
        case SHAVITE:   hash_x16rs_func_8 ( stephash ); break;
        case SIMD:      hash_x16rs_func_9 ( stephash ); break;
        case ECHO:      hash_x16rs_func_10( stephash ); break;
        case HAMSI:     hash_x16rs_func_11( stephash ); break;
        case FUGUE:     hash_x16rs_func_12( stephash ); break;
        case SHABAL:    hash_x16rs_func_13( stephash ); break;
        case WHIRLPOOL: hash_x16rs_func_14( stephash ); break;
        default SHA512: hash_x16rs_func_15( stephash ); break; // case 15
    }
}
```

- [Related algorithms link↗](https://github.com/hacash/x16rs/blob/7b2afef3bafb0a6d7f7301b04230dfa00ed52508/x16rs.c#L444)

This randomness is fundamental to resisting or delaying the emergence of FPGA and ASIC professional hardware mining equipment, and it is also the reason why GPU graphics card mining does not have a significant efficiency advantage over CPU devices for the time being. Because the combination of algorithms executed each time is different, and these algorithms also have different time or space complexity, this huge randomness is also the reason why the computing power statistics of a single HAC mining device fluctuate greatly and cannot obtain stable values.

Both HAC and HACD mining are based on the X16RS algorithm.

## HAC Mining

The difficulty rules for Hacash block mining are the same as for Bitcoin, which is represented by the number of leading zeros of the block hash.

The block hash value of Hacash is serialized by the block header data,  first by a SHA3_256 calculation,and after calculated by multiple X16RS. the block hash related code is calculated: [Related algorithms link↗](https://github.com/hacash/x16rs/blob/7b2afef3bafb0a6d7f7301b04230dfa00ed52508/x16rs.go#L76)

In other words, mining HAC is divided into two steps:

1. Calculate the SHA3_256 hash of the block header; 
2. Perform several more X16RS hash calculations on the previous hash result.

Moreover, the number of computations of X16RS is not fixed, it increases with the growth of block height, starting from 1 time, increasing every 50,000 blocks for about half a year, and remaining unchanged after 16 hashes: [Block Hash Repeat Calculation ↗]( https://github.com/hacash/x16rs/blob/7b2afef3bafb0a6d7f7301b04230dfa00ed52508/x16rs.go#L68), the number of mining times in the first 8 years is changing, and this design is also designed to resist the emergence of professional mining equipment such as ASICs in the early days.

Hacash's block header contains the following fields:

```golang
type HacashBlock struct {
	// head
	Version          VarUint1
	Height           BlockHeight
	Timestamp        BlockTxTimestamp
	PrevHash         Hash
	MrklRoot         Hash
	TransactionCount VarUint4
	// meta
	Nonce            VarUint4 // Mining random value
	Difficulty       VarUint4 // Target difficulty value
	WitnessStage     VarUint2 // Witness quantity level
}
```

- [Related algorithms link↗](https://github.com/hacash/core/blob/425d0f2cff5a747c448ffd1d0b091f37de6af082/blocks/version1.go#L16)

The fields related to mining are 'Nonce' and 'Difficulty', where Difficulty is the compressed value of the target hash difficulty of the block, and Nonce is the random factor (mining randomness) of hash collision mining, both of which are 4 bytes long.

Obviously, as in the case of Bitcoin, when the Hacash mining difficulty rises to a certain scale, the 4-byte nonce value as a random factor will not be able to meet the difficulty target of block mining. In this case, the Merkle root (MrklRoot) of the transaction set comes into play: by changing the order of the transactions, inserting the transaction containing random data, or modifying the MinerNonce value (32 bits) of the Coinbase transaction (fixed to the first coin transaction in the block transaction list), the MrklRoot value can be changed to regain the Nonce nonce space. Each change of MrklRoot gives 256^4 collision attempts (one uint32 value range).

This will cause mining rigs with strong computing power to communicate with full nodes or mining pools to obtain new block header data (referring to the new MrklRoot) after trying a nonce space in order to continue mining. Of course, you can also load the tx_hash of the block header, the tx_body of the Coinbase transaction in the block, and the  of other transactions to the mining device, and the miner will replace the MinerNonce value in the Coinbase transaction by itself, and calculate the new MrklRoot, so that you can obtain unlimited Nonce mining space and avoid repeated and cumbersome communication. However, the miner still needs to keep connected to the mining pool or full node, because it needs to listen to the message of the successful mining of new blocks in the whole network, so as to switch to the next block of mining in time, which means that the lower the network latency between the mining pool and the mining machine, the mining pool and the whole network, the better, otherwise it will affect the mining efficiency, and if there is a disconnection problem, the mining of the equipment may become meaningless.

A MinerNonce can be an arbitrary random number that is used to extend the Nonce space of the mining block header, or to accompany other mining information. Structure of the Coinbase transaction and MinerNonce location:

```golang
type TransactionCoinbase struct {
	// type
	Address Address
	Reward  Amount
	Message TrimString16
	// Version number
	ExtendDataVersion fields.VarUint1 // Equal to 0 before 220000 height
	// When extenddataversion >= 1, it has the following fields:
	MinerNonce   fields.Bytes32
    /* other fields */
}
```

- [Related algorithms link↗](https://github.com/hacash/core/blob/425d0f2cff5a747c448ffd1d0b091f37de6af082/transactions/coinbase.go#L16)

#### Difficulty adjustments

Hacash's block mining difficulty is adjusted to every 288 blocks (i.e., one day), which helps to respond more quickly to changes in the scale of computing power. Each difficulty adjustment can vary no more than 4 times the current difficulty, or no less than 1/4 of the current difficulty.

[Block difficulty adjustment code↗](https://github.com/hacash/mint/blob/909621f84f4873445064d89be24503082c026266/difficulty/algo_v2.go#L97)


## HACD Mining

The casting of HACD is divided into two parts: 1. Mining; 2. Bidding;

This document only deals with the mining part, for the content and description of HACD bidding, please consult another document: [HAC and HACD Mining Fairness Description ↗](https://github.com/hacash/doc-chinese/blob/main/tech/HAC_HACD_mining_fairness_description.md)

The mining mechanism of HACD has been briefly described in the Hacash whitepaper, which is divided into two steps: mining and difficulty checking.

#### HACD Mining preliminary

HACD's mining algorithm starts with a 32-bit hash calculated by SHA3_256 of binary data that contains the current diamond number, the previous block address containing the diamond, the miner address, the random number, etc.:

```golang
// get 32byte hash stuff
func Diamond(diamondNumber uint32, prevblockhash []byte, nonce []byte, address []byte, extendMessage []byte) ([]byte, []byte, string) {
	repeat := HashRepeatForDiamondNumber(diamondNumber)
	stuff := new(bytes.Buffer)
	stuff.Write(prevblockhash)
	stuff.Write(nonce)
	stuff.Write(address)
	stuff.Write(extendMessage)

	/* get ssshash by sha3 algrotithm */
	ssshash := sha3.Sum256(stuff.Bytes())
	/* get diamond hash value by HashX16RS algorithm */
	reshash := HashX16RS(repeat, ssshash[:])
	/* get diamond name by DiamondHash function */
	diamond_str := DiamondHash(reshash)

	return ssshash[:], reshash, diamond_str
}
```

- [Related algorithms link↗](https://github.com/hacash/x16rs/blob/7b2afef3bafb0a6d7f7301b04230dfa00ed52508/x16rs.go#L149)

HACD name is 6 letters, it is derived from the 16 letters of `WTYUAHXVMEKBSZN` and is not repeated, so its fixed total is 16^6 = 16,777,216. Hacash created the 'diamond_hash' hashing algorithm, which computationally maps a 32-bit hash value into a 16-bit hash string result, such as `VMEKUBIAHXSZNWTY`, `IAX0K0VM00EUBZWY` or `0000VMEKUBIAXZWY`, etc.

A HACD is considered to have been mined if and only if the first 10 bits of the 16-bit result string are '0' and the last 6 characters are not '0' (e.g., `00000000000YKUBIA`). Take out the last 6 characters `YKUBIA`` as the literal value of this HACD.

```C
// input length must be 32
static const uint8_t diamond_hash_base_stuff[17] = "0WTYUIAHXVMEKBSZN";
void diamond_hash(const char* hash32, char* output16)
{
    uint8_t *stuff32 = (uint8_t*)hash32;
    int i, p = 13;
    for(i = 0; i < 16; i++){
        int num = p * (int)stuff32[i*2] * (int)stuff32[i*2+1];
        p = num % 17;
        output16[i] = diamond_hash_base_stuff[p];
        if(p == 0){
           p = 13; 
        }
    }
}
```

- [Related algorithms link↗](https://github.com/hacash/x16rs/blob/7b2afef3bafb0a6d7f7301b04230dfa00ed52508/x16rs.c#L578)

Through the above two-step algorithm, after mining a HACD, it does not mean that it can be directly confirmed on the chain, and there are two other steps that need to be completed:

1. Repetition check
2. Difficulty check

Repetition check means that the literal value of the HACD mined by the preliminary algorithm may be the same as the HACD that has been mined, and the second mined HACD can only be discarded and can never be confirm on the chain. In the later stage, as more and more HACD is mined, the probability of repeated mining will increase, which will slowly affect the space of the effective hash value, thus as an aspect of increasing the difficulty of mining.

The difficulty check is a function that checks the validity of the hash value, which is related to the number of the HACD, the higher the number, the higher the difficulty of the target:

```golang
func CheckDiamondDifficulty(dNumber uint32, sha3hash, dBytes []byte) bool {
	var DiaMooreDiffBits = []byte{ // difficulty requirements
		128, 132, 136, 140, 144, 148, 152, 156, // step +4
		160, 164, 168, 172, 176, 180, 184, 188,
		192, 196, 200, 204, 208, 212, 216, 220,
		224, 228, 232, 236, 240, 244, 248, 252,
	}

	// Referring to Moore's law, the excavation difficulty of every 42000 diamonds will double in about 2 years,
	// and the difficulty increment will tend to decrease to zero in 64 years
	// and increase the difficulty by 32-bit hash every 65536 diamonds
	shnumlp := dNumber / 42000 // 32step max to 64 years
	shmaxit := 255 - uint8(dNumber/65536)
	for i := 0; i < 32; i++ {
		if i < int(shnumlp) && sha3hash[i] >= DiaMooreDiffBits[i] {
			return false // Check failed, difficulty value does not meet requirements
		}
		if sha3hash[i] > shmaxit {
			return false // Check failed, difficulty value does not meet requirements
		}
	}

	// Every 3277 diamonds is about 56 days. Adjust the difficulty 3277 = 16 ^ 6 / 256 / 20
	// When the difficulty is the highest, the first 20 bytes of the hash are 0, not all 32 bytes are 0.
	diffnum := dNumber / 3277
	for _, bt := range dBytes {
		if diffnum < 255 {
			if uint32(bt)+diffnum > 255 {
				return false // Difficulty check failed
			} else {
				return true
			}
		} else if diffnum >= 255 {
			if uint8(bt) != 0 {
				return false // Difficulty check failed
			}
			// to do next round check
			diffnum -= 255
		}
	}

	return false
}
```

- [Related algorithms link↗](https://github.com/hacash/x16rs/blob/7b2afef3bafb0a6d7f7301b04230dfa00ed52508/x16rs.go#L254)

In terms of algorithm details, HACD's mining difficulty adjustment mechanism is divided into five parts, which are mixed for the calculation of the difficulty standard:

1. The number of hashes increases about half a year for every 8192 HACD minted, from 1 hash calculation in first to 16 hash calculation in 8 years, and continued to increase until 2048 hashes. Equivalent to the difficulty of computing power will increase by 16 times in the first 8 years;
2. It takes about 2 years for every 42,000 HACD to be minted, and the mining difficulty becomes twice the current mining difficulty, that is, the difficulty factor × 2 . But then the multiplier for each difficulty adjustment will decrease slightly by a factor of less than 2, until it finally decreases to 1 after 64 years. The specific changes are likely to be: 2.00, 1.94, 1.88, 1.83, 1.78, 1.73, 1.68, 1.64, 1.60, 1.56, 1.52, 1.49, 1.45, 1.42, 1.39, 1.36, 1.33, 1.31, 1.28, 1.25, 1.23, 1.21, 1.19, 1.16, 1.14, 1.12, 1.10, 1.08, 1.07, 1.05, 1.03, 1.02, The final coefficient is 1, then the difficulty does not increase。
3. About 56 days after 3277 HACD are minted, the difficulty coefficient will be adjusted by 'one byte', that is, the left byte of the difficulty hash will be reduced by one. This kind of difficulty adjustment changes according to an exponential curve. At present, it is impossible to calculate the specific multiple, and the increase in difficulty in the early stage is not large, and it is not even easy to detect. However, with the increase in the number of HACD minted, the difficulty will increase, and the difficulty increase will gradually become significant in the middle period of mining, which will surpass the mining difficulty of Bitcoin. In the later stage of mining, The increase is increasing until it exceeds the computing power of all computing devices on the planet. In the final stage, the mining difficulty will increase to a mathematical limit, to the point where it is difficult to dig out a diamond even if the energy of the sun is exhausted.
4. Since the literal value of HACD is composed of 6 letters, the total upper limit is 16,777,216. The randomness of the hash calculation leads to the fact that the confirmed literal value of diamonds can be mined repeatedly, so this calculation is also invalid. This will compress the space for effective mining. Although it has little effect on the difficulty in the early stage, when half of the HACDs are minted, the difficulty will increase to 2 times, and then the remaining half of the HACDs will be minted, difficulty increase by 4 times. and then minted in half, and increase the difficulty to 8 times. Every time half of the remaining diamonds are dug up, the difficulty will double, and so on, until the last batch of diamonds is dug up, at this time, almost all the miners have a high probability of mined the duplicate literal value, and mined new valid HACD characters will be very difficult, and the difficulty will grow to the extreme of the impossible.

The combination of the above five dimensions makes the adjustment of the difficulty of diamond mining difficult to show with a simple parameter curve, and can only rely on mining tests and statistics with miners. Unless someone with extremely high mathematical ability and is a programmer at the same time can convert this compound difficulty adjustment mechanism into a mathematical formula, and finally make a chart for everyone to view.

Each HACD must pass this difficulty check function before it can be submitted to the mainnet for bidding, and the mining difficulty is only related to the number of HACD that has been mined, and has nothing to do with the scale of mining equipment investment in the current cycle.
