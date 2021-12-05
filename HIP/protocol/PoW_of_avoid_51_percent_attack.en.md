HIP-7: PoW consensus of avoid 51 percent attack (Beacon Tower Protocol)
===

As we all know, 51% hashrates attack (51 attack) is the main flaw of the PoW consensus protocol. The large scale and stability required by the modern financial currency system cannot tolerate the potentially huge financial risks caused by such serious systemic defects, which greatly hinders the pace of human migration to a more fair and efficient encrypted currency and encrypted financial system. We urgently need an improved consensus protocol that can avoid such defects without losing any of the fundamental benefits of the PoW mechanism.

Such an optimization scheme is not impossible. The reason is that the "longest chain principle" first invented by Bitcoin is only a primitive and crude primary plan. Due to the lack of practical experience data and supporting evidence, and the lack of comprehensive and complete considerations, this inevitably leads to such a situation. Now that more than ten years have passed, we can find a more sound method with the support of enough theoretical attempts and practical experience.

Hacash's white paper has already proposed a solution called "Historical Witness Anti-51% Attack", which describes its theory and general approach, but does not give detailed implementation details. This proposal will follow and extend the existing content of the white paper, analyze the details and consequences of this attack-type defect in detail, propose a more specific and multi-level solution, and explain its precautions and potential costs.

### Attack principle analysis

The 51% attack is one of the biggest obstacles to the large-scale popularization and use of cryptocurrencies. Especially for some newly created cryptocurrencies with small hashrates, the potential attack risk will kill the new currency in the cradle,  although it has fairer and more efficient mechanism design . The market is thus dominated by first-mover advantage, and we have to endure old currencies for a long time.

The principle of this kind of hashrates attack is: a miner (or a very small number) quietly uses a hashrates higher than the total of all other miners to calculate a longer chain privately, but does not announce the chain, and does not allow anyone knows. After a period of time (for example, a few blocks), a longer chain is suddenly broadcast to the entire network. Due to the "longest chain principle" of the consensus protocol, everyone is forced to abandon the recognized chain and switch to the attacker's fork. so that the attacker can withdraw the transaction that is considered to have taken effect (double spend). The attack manifested itself as the invalidation of the mined blockchain and the related transactions were also forced to be cancelled.

In other words, 51% attacks are essentially tampering with history: using greater hashrates to tamper with the history of a block that has been recognized by everyone.

For miners, before judging whether the blockchain they are mining is valid, it only depends on whether it is the longest chain. But the problem is that due to the open access of PoW and the off-chain cost system, the scale of the computing power of the entire network changes at any time. At a certain moment, it is impossible to 100% determine whether there is a private fork chain that is being quietly mined. Whether the current chain is the longest chain is a blind box problem that can't be determined at all. This causes the PoW consensus to never reach the "endgame" and always faces the risk of being rolled back at any time. Bitcoin does not solve this problem, but hopes that the total computing power will reach a very high level, so that no attacker can control more than 51% of the computing power, thereby economically preventing the occurrence of such attacks. But this is not realistic for other smaller altcoins, especially start-up projects.

### Solution

The premise of an effective attack is that the vast majority of all nodes in the entire network have given up the widely broadcast and recognized chain, and are forced to recognize the longest chain that the attacker has secretly mined and suddenly submitted after a period of time. The block and contained transactions that have been deemed valid and executed are cancelled.

The only difference between an attacker and an honest miner is whether to broadcast the newly mined block to the entire network immediately and in a timely manner. If no one conceals the newly mined block, there is no attack. Then the key to the problem lies in how to force everyone to broadcast blocks in time from the benefit or punishment mechanism, or to design an identification mechanism or chain selection protocol so that the concealed blocks are not recognized by everyone.

This means that we must abandon the deeply rooted "Longest Chain Principle" and replace it with the "Publicity of the Widest Chain Principle."

Distribute the judgment power of the effective chain from the miners to the full nodes of the entire network. Miners dig out blocks and broadcast them, and the nodes of the entire network supervise in real time and jointly judge whether the mined blocks have been published in a timely and extensive manner.

Fortunately, unlike the "longest chain", the "published widest chain" is a judgment rule that can reach the "endgame" through some kind of communication or agreement. We only need to design an agreement that allows the vast majority of honest nodes to reach a consensus, and then definitely jointly select the "broadest chain" with the widest broadcast, and unanimously resist the "longest chain" submitted by individual attackers. Potential attackers anticipate that the privately mined chain will not be recognized by the majority of the entire network, so this kind of attack will not occur. In other words, we can assist the effective operation of the consensus protocol through social cooperation such as "off-chain communication cooperation," instead of relying all hope on code.

Of course, this solution is not perfect. It is based on some basic premises and assumptions, and it also brings some additional costs or higher operating requirements. We will analyze it in detail later. But in any case, compared to the major core risk of being attacked by double spending, it is necessary and completely worth the cost to pay for this. There is no free lunch in the world.

### First layer solution

Hacash's target block interval is about 5 minutes. However, due to the strong random nature of the X16RS mining algorithm, it often happens that the next block is dug out after more than ten seconds. Since it takes time to verify the validity of the block and broadcast, it occasionally happens that two miners dig out the next block almost at the same time. At this time, the entire network will receive two valid blocks, and half of the full nodes will receive different block, which caused a de facto temporary block chain fork.

Assuming that the average block interval time is 5 minutes or 300 seconds, the probability of digging two valid blocks in the same second is 1/300, so if four consecutive blocks are mined at the same time, the average probability is ( 1/300)^4 is approximately equal to 0.00000000012345679. The probability is less than one in ten billion, which means that it will only appear once in 10,000 years. This is safe enough.

It takes an average of 20 minutes for 4 blocks to be mined, and it usually only takes a few seconds to broadcast a block filled with transactions to the world under normal circumstances. An active and honest miner does not need 20 minutes to broadcast newly mined blocks anyway.

Therefore, the first plan is to add a confirmation checkpoint with a length of 4 blocks. All nodes in the entire network will not accept the fork of the 4 blocks before, and the default is that this historical fork was submitted by a dishonest attacker and refused to accept it. In other words, for a full node with a smooth network connection, the block before the 4th block can be regarded as permanent confirmation and irreversible.

### Second layer solution

Setting the checkpoint of 4 blocks still leaves about 20 minutes of "returnable time". In theory, the latest 4 blocks are all immature and unconfirmed. Moreover, this checkpoint is only valid for the node itself, and cannot achieve a comprehensive end. In other words, if a miner or a certain geographic area is disconnected for a long time and more than 4 blocks are dug out, the 4 block checkpoint scheme will cause partial permanent forks, affecting the affected The node has to manually judge which one is recognized as a valid chain, and manually select the fork and fallback block. However, it should be noted that no consensus scheme can handle the problem of large-scale and long-term network disconnection. The only difference is that the consequences are different: either fork or stop generating blocks altogether.

We propose a second-level solution, which is to allow certain selected addresses or nodes to vote for the newly mined block (for example, select 21 miner addresses of the most recently mined blocks as voters). The list of addresses eligible to vote is limited and certain. If the result of the vote is more than half, for example, 11 of the 21 recent miners have voted on a block, indicating that the block has been received and approved, then the entire network It can be considered that this block has been broadcast to almost all nodes, and a final consensus has been reached, so that there is no need to wait for the automatic confirmation of 4 blocks.

Since the voting data is much smaller than the block, and no other calculations are required except for a private key signature verification, the broadcast speed of the voting data will be much faster than the block broadcast, so basically as long as the block reaches the full node, it can be immediately Confirmed by vote. In other words, as long as voters actively participate in voting, the final confirmation time of the block will depend on the speed of the block broadcast, which is about a few seconds.

Since voting requires a private key to sign online in real time, this will result in a decrease in security. We can use delegated voting to solve the problem that some miners are not motivated to vote or cannot maintain real-time online, that is, delegate voting rights to a certain address that everyone chooses and has no assets on the account. Even if the address is stolen, there will be no loss of assets, and voting can proceed as usual. The only consequence is that there may be double voting, which is not a problem. You can delegate a new address again.

These voters selected according to fixed rules can be called "Beacon Tower on the chain". As long as most of the "Beacon Tower" lights up fireworks (voting), we can consider this block to be permanently confirmed. The blocks of the same height that are broadcast in the future are the behavior by dishonest attackers.

### Third layer solution

The second-layer scheme still has a flaw, that is, when attackers have 51% of the computing power, they can pretend to be a majority of new miners and do not participate in voting, so that the block cannot be confirmed instantly. But this also brings another benefit, that is, attack early warning. In other words, when the majority of voters suddenly do not participate in voting, it means that an attack may occur on the network, and everyone will be able to prepare for it immediately.

At this time, the third-tier scheme can be introduced, that is, the "off chain Beacon Tower" consensus. These off-chain voters are not selected by algorithms according to certain rules, but rely on the trust and prestige of the community, and are scattered in various physical locations around the world. The longer chain submitted by the attacker afterwards must be much later than the honest chain in terms of broadcast time. Generally, the blocks of submit by ​attacker will be dozens of minutes to several hours late for the attack to be effective.

The "Beacon Tower off-chain" recognized by the community will continue to record every block it receives and the time of receipt. When an attack occurs and the second-level scheme is invalid, the nodes of the entire network can combine the local 4 block confirmations and the information released by these "community beacon towers" to comprehensively determine which chain is the attacker's behavior, and then jointly resist it.

Through the above three schemes, 51% of attackers will expect that the success rate of the attack will be very low. In a network where the connection is kept open, the attack will be quickly and effectively identified. As a result, the attack will not occur.

### Problems and costs

Although the above solutions can effectively solve the problem and eliminate the financial risks caused by serious double-spending attacks, it is not 100% perfect, and it require certain basic operating conditions and brings certain consequences:

* Higher requirements are placed on the quality of the network connection of miners (stability rather than speed). Miners cannot be disconnected, especially miners with a relatively large amount of computing power. The disconnection will cause the actual blockchain bifurcation and even more Rely on manual intervention. This is because the protocol cannot identify those who are honest miners who just disconnected from the network, and who are malicious and secretly mining attackers, all of which are treated equally as malicious.

* The overall block chain’s resistance to large-scale physical attacks (such as war) is reduced. The entire network cannot only technically judge which chain is the effective chain, but must rely on maintaining follow-up judgments on real-time information and social, Community cooperation. Newly participating nodes cannot be separated from the real-time information of the community, and  cannot determine which chain is valid only from the block data itself.
































