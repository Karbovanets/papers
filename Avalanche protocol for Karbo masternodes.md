
# Avalanche protocol for Karbo masternodes

To prevent double spend by cancellation of transactions in 51%-attack we introduced a **transaction comparison** during reorganization to the alternative chain: *“an alternative chain should contain all transactions from the chain it is to replace”.* In the case of a reorganization nodes compare transactions in the alternative chain and in their current main chain and if at least one transaction their chain is missing in the alt. chat, they reject the reorganization. Missing transactions will be added into the mempool of the nodes proposing the alternative chain so they have chance to include those transactions into the next block then nodes on the main chain will reorganize to theirs chain and the reorganization will take place.



This is effective protection against double spend attacks. However, in edge cases it may lead to the permanent chain split, for example due to communication latency between nodes on different parts of the network, or temporary network partition due to communication interruption. In standard CryptoNote protocol the situation with two competing chains is resolved by the *‘longest chain’* rule: nodes always choose the longest chain with the most work. With the introduction of transaction comparison this no longer works. If there is missing transaction, nodes from both parts of the network will refuse to reorganize. This can happen in two cases:

1.  The first case is double spend, when attacker sends same coins in two network partitions to different destinations.
    
2.  The second case is when miners in both chains immediately spend their newly mined coins after they become unlocked. The possibility of second case will be greatly reduced by increasing of the mined money unlock window from 10 blocks to 100.
    
 

To resolve such chain split it is proposed to use *Avalanche* protocol [1] in the voting for the checkpoint and valid chain. The protocol works like this: when node is on chain A and encounters longer alternative chain B and it can not reorganize to chain B due to the missing transaction, it selects 5 random masternodes and polls them for the checkpoint of their chain. If 3 of 5 nodes are on chain B, then node reorganizes to chain B overriding transactions comparison. If the majority of masternodes in polling group are on chain A, node stays on its chain A and does not reorganise.



The masternodes do the same voting procedure among themselves. This allows nodes to quickly reach consensus on the correct chain, which almost certainly will be so called ‘public’ chain, with majority of honest nodes.



To prevent the ability of the attacker to spawn the large number of his nodes to fake the voting in the vafor of his chain, in voting process can only participate nodes with collateral, which we call *masternodes*. This will significantly increase the price of an attack attempt, or even make it nearly impossible if collateral is large enough, as attacker will have to possess the substantial amount of coins to win the voting.



The masternodes register themselves on blockchain with special transaction that has `extra` fields, containing masternode’s IP, and `reserve_address`.


In voting can only participate masternodes, registered *before* the split. This ensures all nodes on both partitions have the same list of masternodes which allows to reach consensus.



Each masternode has special Json RPC handle `/collateral` with such fields in response:

-   `reserve_address` – public address (the same address as for receiving fees is used);
    
-   `reserve_proof` – the Proof of reserve certificate.
    


To prevent reuse of the same money for multiple masternodes we set these rules:

-   the challenge message field of the `reserve_proof` must contain the IP address of the masternode;
    
-   the `reserve_address` of the masternode must be unique across the network.
    


Before adding new masternode to the list of masternodes, or querying masternode for the checkpoint, node performs the verification of the masternode’s collateral in the following order:

1.  It validates the `reserve_proof` against provided `reserve_address` of the new masternode.
    
2.  It uses masternode’s IP for the verification `message` field. If IP does not match, the verification fails;
    
3.  Only if `reserve_proof` is valid for the given `reserve_address` and masternode’s IP, and the unspent balance is sufficient, node checks if this `reserve_address` is already used for any masternode that is on the list. If `reserve_address` already exists on the list, then new masternode is not added. (It is possible to extend this check with punishment by removing both masternodes with duplicate `reserve_address` from the list).
    


When querying masternode for the checkpoint, node does the verification of the `reserve_proof` in the same way as above. It checks the reserve proof with reserve address and IP of the masternode. If IP or address does not match, or balance is insufficient, the verification fails and masternode is not included into polling group and is removed from the list of masternodes. In this case node selects next random masternode from the list and repeats verification. If chosen masternode passes the verification, it is added to the polling group.



When node has 5 masternodes in polling group it queries them for their last checkpoint, and performs the reorganisation to the chain on which are 3 masternodes of 5.



This procedure allows quickly reach consensus on correct chain. We guesstimate that consensus will be reached in few seconds across the network.




References


[1] https://ipfs.io/ipfs/QmUy4jh5mGNZvLkjies1RWM4YuvJh5o2FYopNPVYwrRVGV
