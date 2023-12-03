Hacash Software Download link and Release log
===

### Download the latest version of software on [hacash.org](https://hacash.org):

1. Hacash full node: 

    [miner_node_hacash_windows64.zip](https://download.hacash.org/miner_node_hacash_windows64.zip)
    <br>
    [miner_node_hacash_ubuntu64.zip](https://download.hacash.org/miner_node_hacash_ubuntu64.zip)
    <br>
    [miner_node_hacash_MacOS_64.zip](https://download.hacash.org/miner_node_hacash_macos64.zip)

2. Miner pool worker:

    [miner_pool_worker_windows64.zip](https://download.hacash.org/miner_pool_worker_hacash_windows64.zip)
    <br>
    [miner_pool_worker_ubuntu64.zip](https://download.hacash.org/miner_pool_worker_hacash_ubuntu64.zip)
    <br>
    [miner_pool_worker_MacOS_64.zip](https://download.hacash.org/miner_pool_worker_hacash_macos64.zip)

3. Miner worker:

    [miner_worker_windows64.zip](https://download.hacash.org/miner_worker_hacash_windows64.zip)
    <br>
    [miner_worker_ubuntu64.zip](https://download.hacash.org/miner_worker_hacash_ubuntu64.zip)
    <br>
    [miner_worker_MacOS_64.zip](https://download.hacash.org/miner_worker_hacash_macos64.zip)

4. Miner relay service:

    [miner_relay_service_windows64.zip](https://download.hacash.org/miner_relay_service_hacash_windows64.zip)
    <br>
    [miner_relay_service_ubuntu64.zip](https://download.hacash.org/miner_relay_service_hacash_ubuntu64.zip)
    <br>
    [miner_relay_service_MacOS_64.zip](https://download.hacash.org/miner_relay_service_hacash_macos64.zip)

5. PC desktop offline wallet:

    [hacash_desktop_wallet_windows64.zip](https://download.hacash.org/hacash_desktop_wallet_windows64.zip)
    <br>
    [hacash_desktop_wallet_ubuntu64.zip](https://download.hacash.org/hacash_desktop_wallet_ubuntu64.zip)
    <br>
    [hacash_desktop_wallet_MacOS_64.zip](https://download.hacash.org/hacash_desktop_wallet_macos64.zip)

6. Command line wallet:

   [hacash_cmdwallet_windows64.zip](https://download.hacash.org/hacash_cmdwallet_windows64.zip)
   <br>
   [hacash_cmdwallet_ubuntu64.zip](https://download.hacash.org/hacash_cmdwallet_ubuntu64.zip)
   <br>
   [hacash_cmdwallet_MacOS_64.zip](https://download.hacash.org/hacash_cmdwallet_macos64.zip)


7. Channel Payment Node Servicer:

   [hacash_channelpay_servicer_windows64.zip](https://download.hacash.org/hacash_channelpay_servicer_windows64.zip)
   <br>
   [hacash_channelpay_servicer_ubuntu64.zip](https://download.hacash.org/hacash_channelpay_servicer_ubuntu64.zip)
   <br>
   [hacash_channelpay_servicer_MacOS_64.zip](https://download.hacash.org/hacash_channelpay_servicer_macos64.zip)

   
8. Channel Payment User Client:

   [hacash_channelpay_client_windows64.zip](https://download.hacash.org/hacash_channelpay_client_windows64.zip)
   <br>
   [hacash_channelpay_client_ubuntu64.zip](https://download.hacash.org/hacash_channelpay_client_ubuntu64.zip)
   <br>
   [hacash_channelpay_client_MacOS_64.zip](https://download.hacash.org/hacash_channelpay_client_macos64.zip)

    
---

### Lastest release log:


#### Version: 0.1.18  (2023.12.03.01)

update log:

> [1] change: RPC API `raise_tx_fee` support txbody.

#### Version: 0.1.16  (2023.10.26.01)

update log:

> [1] new: RPC API `scan_coin_transfers`.
> [2] fix: drop DiamondHash X16RS repeat 16 max limit.

#### Version: 0.1.15  (2023.03.09.01)

update log:

> [1] fix bug: miner pool open bug.

#### Version: 0.1.13  (2022.09.09.01)

update log:

> [1] new: Accurate version of hashrates statistics.

#### Version: 0.1.12  (2022.01.25.01)

update log:

> [1] add: Diamond miner automatic bidding supports custom inspection time, the minimum support is 0.1 seconds, config field is `autobid_check_interval`, unit is seconds.<br>
> [2] fix bug: Some blocks packaged by miner cannot contain any transactions.

#### Version: 0.1.11  (2022.01.18.01)

update log:

> [1] add: MacOS support!<br>
> [2] fix bug: isChangeCurrentForkHead cannot change refer height.<br>
> [3] new: add action support 'Action_29_SubmitTimeLimit'.

#### Version: 0.1.10  (2021.12.10.01)

update log:

> [1] fix bug: Miner cannot pack and mine blocks containing transactions.

#### Version: 0.1.9  (2021.12.08.01)

update log:

> [1] new: rewrite the blockchain core engine.<br>
> [2] new: HIP-7 "The First Layer Solution" base development and test.

#### Version: 0.1.8  (2021.11.21.01)

update log:

> [1] fix bug: RecoverDatabaseVersionRebuildMode.

#### Version: 0.1.7-repair2  (2021.11.17.02)

update log:

> [1] FIX FORK OF BLOCK HEIGHT 292715!

#### Version: 0.1.7  (2021.11.1)

update log:

> [1] fix bug: Channel payment blockchain mainnet arbitration action support open.<br>
> [2] fix bug: Diamonds miner difficulty adjustment.

#### Version: 0.1.6  (2021.10.27)

update log:

> [1] new: Channel payment blockchain mainnet arbitration action support.<br>
> [2] new: Channel payment NodeServicer & UserClient desktop software.

#### Version: 0.1.5  (2021.7.19)

update log:

> [1] change: PoW hashrate calculation.<br>
> [2] fix bug: whole network miner stop.<br>
> [3] test code: system & user lending testing.

#### Version: 0.1.4  (2021.5.15)

update log:

> [1] fix bug: diamond miner autobid invalid.<br>
> [2] fix bug: verify address 1M5qxL6zZANiHNfR8XrKrJjifgjeaVzG8g signature fail.

#### Version: 0.1.3  (2021.5.6)

update log:

> [1] fix bug: TrimString16 overflow length bug.<br>
> [2] new: Open Payment Channel at desktop wallet.<br>
> [3] new: Sign tx and check tx content at desktop wallet.<br>
> [4] chg: BTC move check logs download and check func.

#### Version: 0.1.2  (2021.4.10)

update log:

> [1] fix bug: Minerpool error powmsg crash.


#### Version: 0.1.2  (2021.3.25)

update log:

> [1] fix bug: P2P module that caused the node to exit incorrectly.


---

#### Version: 0.1.1 (2021.3.11)

need resync block data, update log:

> [1] new: HACD biding fee burning of 90%.<br>
> [2] chg: TxFee purity calculate optimization.<br>
> [3] chg: Channel interest calculate fix uint64 overflow bug.<br>
> [4] new: Rpc api add current circulation and more total supply count.





_end_
