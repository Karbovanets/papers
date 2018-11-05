Typical 51% attack on the cryptocurrency exchanges can be boiled down to the cancelling of the transaction during a switch to the alternative chain. To be able to cause a blockchain reorganization the malicious actor will need to gain control of more than 50% of a network’s hashrate, hence the name.

Put simply, the attacker submits to the network a transaction in which deposits coins to the exchange, while privately mining a blockchain fork in which a double-spending transaction is included instead or a deposit transaction is just absent. After waiting for *n* confirmations, the exchange credits coins to attacker account. He sells deposited coins and withdraws other currency. If the attacker happened to find more than *n* blocks at this point, he releases his fork and regains his coins; otherwise, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this, the attack fails, the payment to the exchange will go through, and the work done mining will also be wasted, as any new coins would be overwritten by the longest chain. [1]

The exchanges can require the large number of confirmations *n*, but if an attacker can continue to mine his alternative chain however long, this method will not protect the exchange. Nowadays, when there is available very large amount of hashrate for rent at attacker’s disposal we need better solution than just raise the *n*.

We propose the solution to disallow cancelling transactions during a switch to the alternative chain by adding the consensus rule stating that “an alternative chain should contain all transactions from the chain it is to replace”.

The protection against 51% attack will then look like this: in the case of a reorganization of the blockchain nodes compare transactions in the alternative chain and in the current main (their) chain and if at least one transaction is missing in their chain they reject the reorganization.

In this case missing transactions will be added into the mempool of the nodes proposing the alternative chain so they have chance to include those transactions into the next block then nodes on the main chain will reorganize to theirs chain and the reorganization will take place if their chain is longer. This allows to unite blockchain in the case of accident splits by the longest chain rule when it is not an attack or in the scenario where transaction is just missing from the attacker’s alternative chain.

In the case of contradicting transactions the nodes will endup in permanent chain split and will require manual intervention as both chains will reject each other because each will lack transaction from the other. In this situation the attacker will permanently lock himself in his alternative reality.

With this rule if there is no contradicting transactions the blockchain can split and rejoin up to the length where the first transaction spending newly mined coins occurs, in other words, the reorganization length can be reduced to the mined money unlock window parameter’s value since miners can protect their chain just by spending mined coins as soon as they unlock. Therefore, to allow larger reorganizations this parameter should be set at reasonable value.

The simple attack on network to cause chain spit: an attacker sends same coins to himself via different nodes and if two nodes include contradicting transactions into different blocks they will go astray each in own chain. To mitigate such attack the comparison of transactions can be triggered not immediately when the reorganisation occurs but if an alternative chain is longer than usual reorganisation which is rarely more than 2 blocks i.e. after the 'grace period'.

The intoducing of grace period opens the way to bypass the comparison by 'move to cancel' attack as described by zakurai from XSH: 51% capable miner can wait for *n* confirmations then do reorg by reincluding transaction he wants to cancel into the last block then do small reorg within the grace period in which transactions are not compared.

Therefore the 'grace period' method is futile. Trailing checkpoint system can address the issue of split chains.

[1] https://en.bitcoin.it/wiki/Irreversible_Transactions
