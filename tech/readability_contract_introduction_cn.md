# Hacash 可读金融合约简介及技术说明

加密货币基础层及现代金融结算系统都需要稳固、安全和人类友好的核心技术，其重点即体现在交易数据结构方面。比特币采用 UTXO 交易模型，简单但局限性大，以太坊支持图灵完备的智能合约，在技术扩展性上做到最高，但其安全性和用户友好性差，除非委托专业程序员的帮助，普通人难以使用。历史上 TheDAO 事件带来的以太坊分叉和回退，也表明智能合约在安全性方面的重大问题。类似事件如果发生在一个国家的货币系统上则将是灾难性和不可接受的。加密行业作为新兴领域，以太坊能持续生存下来，多数是因为大家给予了更多的宽容。

Hacash 创造了“可读金融合约”这一技术，来权衡区块链交易数据结构的安全、扩展性和易用性这三个不可能一同达成的维度。通过舍掉一些非必要特征（主要就是图灵完备的可编程性），从而在安全和人类友好易用两方面，和金融支付的灵活强大性上做到极致。

本文档着重于在技术层面理解 Hacash 可读金融合约的技术原理及交易结构，通过创造两个简单的合约示例（1、HAC和HACD的原子互换合约；2、HAC遗产继承合约）来介绍可读金融合约的特点及原理，帮助有需要的开发者创造更多更丰富的自定义合约。

### 一、Hacash 的交易数据结构

Hacash 的交易结构可以分为三个部分，如下：

```go

type Transaction_2_Simple struct {
	
	// 交易头 
	Timestamp   fields.BlockTxTimestamp
	MainAddress fields.Address
	Fee         fields.Amount

	// 交易内容表单
	ActionCount fields.VarUint2
	Actions     []interfaces.Action

	// 签名
	SignCount fields.VarUint2
	Signs     []fields.Sign

	MultisignCount fields.VarUint2
	Multisigns     []fields.Multisign
}

```

其中，“交易头”数据分别为时间戳、主地址（也是手续费支付地址）和手续费数量。Hacash 的交易结构天然支持隔离见证和第三方支付转账手续费，而这两项功能在比特币和以太坊上需要通过 taproot 升级和 EIP4337 提案部署通过才能实现。

Hacash 的“可读性合约”的主要实现在交易结构的第二部分“交易内容表单”里实现。简单来说，可读金融合约跟实际经济生活的签署的合同没有太大区别：都是一些支付和条件的条目组合，形成一个不可分割的整体事务。Hacash 通过“合约解释器”来验证和执行提交的可读金融合约，如果发现合约内部有任何一个条件不满足（比如某个付款方的余额不足），则整个合约都不能执行和上链确认（类似关系型数据库的事务机制）。

“Actions” 表示一系列支付动作或支付条件的集合列表，所有涉及到这些支付的授权方（例如支付方）都需要一起参与签名，缺一不可（收款方不需要签名），整个合约才能通过区块链的签名审核。然后，区块链状态机会验证 Actions 内部的每一个支付条件和动作，检查余额、区块高度等列出条件，任何一个条目不符合，整个合约就会被退回。也就是说，整个合约的所有子条目，要么同时成功，要么一起失败，不存在一些支付成功了而另一些却失败的异常情况。

“Actions”目前支持31种支付条目，涉及 HAC、HACD和BTC的支付、二层通道开通及关闭、区块高度条件等，可以采用这些支付条目任意组合，完成更丰富灵活的支付需求：

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

更多代码详见：https://github.com/hacash/core/blob/b3bfca2fc4a3ed3ad241e315fa51529b421cee53/actions/action.go#L11

这些支付条目和支付条件，可以被做成UI可视化、可读文本化的形式来表达，而不是通过专业程序员才能理解的代码逻辑来提现，条目之间是可以任何组合，用户可以像签署一个商业合同一样轻松且准确的检查每一项支付和条款，每项支付都可以用人类友好型的自然语言表达出来，所见即所得，没有潜在漏洞或其他安全问题。接下来，我们将通过这些支付条目的组合，完成两个最简单的合约示例。

### 二、HAC和HACD的原子互换合约（DEX）

根据上文所述的“可读金融合约”的技术原理，要在 Hacash 上实现一个 HAC、HACD和BTC三者任意互换的去中心化交易所，将特别简单，无需进行任何类似其他公链的智能合约代码的编写，而只需要开发一些前端UI的操作界面，帮助客户将若干个 Action 组合起来，并提交上链即可。

假设地址 1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9 要用 120HAC 的价格向地址 1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY 购买 3 个 HACD，则此 DEX 的订单只需要两个 Action 条目， 采用JSON格式的伪代码来表示这个合约：


```javascript

{
	timestamp: "2023-08-29 18:59:30",
	main_address: "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9",
	fee: "0.0001HAC",
	
	action_count: 2,
	actions: [{
		// 合约条目类型 1， 表示 main_address 向 to_address 支付 amount 的 HAC 
		act_id: 1,
		amount: "120HAC",
		to_address: "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY"
	},{
		// 合约条目类型 6， 表示  from_address 向 too_address 支付 diamond_list 里面的 HACD
		act_id: 6
		diamond_list: ["NHMYYM","WKEBKX","SSIKZM"],
		from_address: "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY",
		to_address: "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
	}],
	
	
	sign_count: 2",
	signs: [
		"xxxxxxxxx....(示意数据)....", // 地址 1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9 对此交易的签名
		"yyyyyyyyy....(示意数据)....", // 地址 1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY 对此交易的签名
	]
}

```

没错，以上就是完成这笔 DEX 原子互换交易的全部所需代码。

我们进一步分析上面的合约会发现，这笔交易的费用需要 HACD 的买家 1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9 来支付，且与我们 DEX 平台没有任何关系。我们现在进一步想要实现由 DEX 运营方（假设服务平台方的地址为 14ExC1ciZ5Mr5xPPLU3kKg5q3Q3Piey1xH ）来支付这笔交易的主网手续费，并且向 买家 收取一笔固定的 HAC 服务费用，这笔交易合约将变成：


```javascript

{
	timestamp: "2023-08-29 18:59:30",
	main_address: "14ExC1ciZ5Mr5xPPLU3kKg5q3Q3Piey1xH", // 主地址变成了 DEX 平台方
	fee: "0.0001HAC",
	
	action_count: 3,
	actions: [{
		// 合约条目类型 13， 表示 from_address 向 main_address 支付 1.5 的 HAC
		//（作为撮合交易的服务费） 
		act_id: 13,
		amount: "1.5HAC",
		from_address: "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
	},{
		// 合约条目类型 14， 表示 from_address 向 to_address 支付 amount 的 HAC 
		// 买家支付给卖家
		act_id: 1,
		amount: "120HAC",
		from_address: "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9",
		to_address: "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY"
	},{
		// 合约条目类型 6， 表示  from_address 向 too_address 支付 diamond_list 里面的 HACD
		// 卖家将 HACD 发货给买家
		act_id: 6
		diamond_list: ["NHMYYM","WKEBKX","SSIKZM"],
		from_address: "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY",
		to_address: "1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9"
	}],
	
	
	sign_count: 3",
	signs: [
		"xxxxxxxxx....(示意数据)....", // 地址 1MzNY1oA3kfgYi75zquj3SRUPYztzXHzK9 对此交易的签名
		"yyyyyyyyy....(示意数据)....", // 地址 1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY 对此交易的签名
		"zzzzzzzzz....(示意数据)....", // 地址 14ExC1ciZ5Mr5xPPLU3kKg5q3Q3Piey1xH 对此交易的签名
	]
}

```

只需要三个支付条目的组合，即在 Hacash 上实现了一个 用 HAC 购买 HACD 且支付给 DEX 服务商一定服务费用，并由服务商提供 gas 费的去中心化交易合约。并且没有任何潜在的漏洞、安全风险，整个交易是原子化的，不存在买家支付了 HAC 却拿不到 HACD 的情况，也不存在被交易服务商扣押资金的情况，你不需要将你的 HAC 或者 HACD 发送到任何第三方的地址里由他控制。而 DEX 服务商只会在交易完全成功时才会收取到应得的服务费。

此交易有关的三方，任何人不完成签名、账户余额不足或者没有拥有指定的 HACD ，交易都将不能达成，且大家都没有任何损失。

### 三、HAC遗产继承合约

这是一个相对更加复杂的例子，因为这涉及到合约之外的操作更新（称之为链外博弈）。

设想一下，你有 10000 个 HAC 想将在你去世后自动转移到你孩子的账户里，且不能够或不信任委托给任何第三方资金管理机构来操作，如何通过 Hacash 的支付合约来实现？

实现方法也很简单，你只需要签署一笔两年之后才能被上链确认的交易即可，定义一个合约，将 10000 枚 HAC 在 2年 以后自动支付给你的孩子：



```javascript

{
	timestamp: "2023-08-29 18:59:30",
	main_address: "1853ySaM6cofnduMHcQQdR7pCQBWwJ9mVU", // 你的地址 
	fee: "0.0001HAC",
	
	action_count: 2,
	actions: [{
		// 合约条目类型 1， 表示 main_address 向 to_address 支付 amount 的 HAC 
		act_id: 1,
		amount: "120HAC",
		to_address: "1P6DHQYjP6WygqTCzwXpwo7TxWqhA1SgVY" // 你孩子的地址
	},{
		// 合约条目类型 29 表示在 指定区块高度之后 此笔交易才能上链被确认
		act_id: 29,
		start_height: 573645  // 平均每 5 分钟一个区块，可以预估两年后的区块高度
	}]

	// 此处省略签名数据...
	
}

```

这笔钱会在2年之后自动转账给你的孩子（他需要保存这笔交易并在两年后提交至主网）。但如果两年之内你并未去世，不愿意现在就让他拿到这笔钱，怎么处理？你只需要在临近 2 年到期之前，将这 10000 个 HAC 转账到一个新的地址，重新签署一笔新的交易即可。也就是说，相当于你要每两年（或者其它你任意定义的时间周期）更新一下你的遗嘱 。

请注意，这时候还并未有任何交易提交到主网。也就是说，这个合约功能是基于“链外博弈”的，只有在合约可以生效的时候才会被上链确认。这是一种新的区块链交易范式，可以极大的节约主网的区块空间（大部分链外合约可能永远不会上链），但仍然完成了在类似以太坊智能合约平台上必须通过部署代码且调用执行代码才能完成的功能。

### 四、总结

未来将有更多的支付条目被支持，也会持续升级合约的基础协议，这意味着你将能创造性的开发出更多、更功能强大的“可读金融合约”，而这一切，都不以牺牲区块链核心层的安全性和易用性为代价。


