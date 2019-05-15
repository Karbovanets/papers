## Blockchain Dependant Hash (BloDHa)
Authors: [Aiwe](https://github.com/aivve/), [Luke](https://github.com/ClashLuke)
### Abstract
BloDHa is a blockchain dependant hash algorithm forcing every miner and validator to run a SPV node or higher increasing security of the network by reducing trust.

### Introduction
#### Concept
The core idea of a blockchain depentant hash algorithm is to sit on top of an existing hash function which already fulfills every requirement a currency has, but add the requirement of storing the entire blockchain and therefore acting a full node that supports the network. 

#### Problems 
- **No Pool Resistance**: Since both pools and miners can store the entire blockchain themselves, there is no resistance against pooling hashrate. It merely evens out the expenses of solo-mining and pool-mining, by forcing every miner to be a full node.
- **SPV Nodes**: If the protocol allows it, SPV nodes are possible to be used in Blodha. The usage of block data other than the header is painful for all involved parties.
- **Botnet centralisation**: A botnet owner is not interested in having one central point or DDoSing a server they need access to.
- **Botnet honesty**: A botnet has to connect to a pool, to be able to attack the network. When connecting to their own pool, instead of a public pool, their anonymity goes down. When connecting to a public pool, the botnet is forced to be honest.
- **Outsourcability**: It's not interesting for a mining rig owner to rent out hashrate for many different algorithms or synchronising gigabytes of data for every task, which would be the case with Blodha. Assuming one or more coins become widely adopted, the security against outsourcability collapses.

### Core
#### Design
To have both the features of an advanced cryptocurrency hashing algorithm, such as assymetry, botnet resistance and ASIC resistance, but also the core feature of Blodha, every miner being a node that previously validated the entire chain, every variant of Blodha has to start with a strong cryptocurrency hash algorithm such as [SquashPoW](https://github.com/ClashLuke/CCLib/tree/master/Squash-PoW).

As a second step, after hashing the header, the hash has to be processed dependant on the current blockchain history. This can be done by splitting the hash (32 byte) into eight groups of four bytes to receive eight 32-bit unsigned integers. Those can then be used as eight height indicators to query block headers, after making sure that nothing gets out of bound by taking the remainder after dividing the indicator height by the blockchain height. By appending these block headers to oneanother and then hashing them, a new hash is generated which can then be used to start this part over again. A hash such as [Blake2s](https://github.com/BLAKE2/BLAKE2/blob/master/sse/blake2s.c) or [CRC32-256](https://github.com/ClashLuke/CCLib/tree/master/CRC256) is recommended, to take the power away from calculation and shift all potential hashrate gains to having a faster hard drive.

Lastly, after a given number of iterations, the final hash is returned. Unfortunately the only assymetric part is the first part, forcing every validating node to host all headers themselves.

#### Considerations
- **Database**: When using hashes instead of headers, a database containing all necessary data would grow at a pace of 32 byte per block resulting in 8MiB/Year with a 2 minute block target. This can be put on GPUs or queried from public blockchain explorers to be then stored in RAM without a botnet node noticing anything. Therefore headers (~2KiB per block) have to be used to have a growth of 538MB/year.
- **No SSD Death**: Since blockchain data is immutable, a SSD storing the blockchain needs one write cycle (more in case of reorgs) and a lot of read operations, which do not harm the SSD. This implies that a cheap 30$ SSD creates huge speedups for a miner.
- **Iterations**: To force a node to store all headers themselves, they have to be forced to query a whole lot of headers. Assuming a minimum ping time of 10ms (RTT of 20ms assuming instant answers), a botnet node can still receive 50H/s without using any resource to its maximum. When increasing the iteration number in the second step to 1000, the hashrate of a botnet node drops to 0.05H/s while a local node receives a relatively low drop. This iteration number has to be tweaked individiually to have the iterative part be as fast as the hashing part on an honest node.

#### Pseudocode
A descriptive python code to know the algorithm without necessarily having to understand it would look like this:

```PYTHON
ITER = 10**6

def blodha(inputData):
	initialHash = hash(inputData)                                                      # Hash meeting all required properties (ASIC resistance)
	for i in range(ITER):                                                              # Iterate ITER times:
		headerBlob = ""                                                            # Initialise an unknown-size list
		for i in range(0,32,4):                                                    # i = 0, 4, 8,..
			headerBlob = headerBlob + getHeaderFromHeight(initialHash[i:i+4])  # Append headers to list
		initialHash = hash(headerBlob)                                             # Very fast hash such as Blake2
	return(initialHash)
```

### Conclusion
Blodha is a strong extension to existing cryptocurrency hash algorithms, but it is nothing else. Blodha is not a new hash and therefore does not provide ASIC resistance, pool resistance or a resistance against Botnets. The only thing it does is change how nodes behave, by forcing botnet participants in being honest and generally adding more SPV and full nodes to the network.

To achieve a strong security against botnets hashing on the network, which in turn will reduce the security of the network and increase the reward for selfish miners, a hash algorithm that is notable on botnet-nodes has to be used as an initial hash (such as [SquashPoW](https://github.com/ClashLuke/CCLib/tree/master/Squash-PoW) or [Ethash](https://github.com/ethereum/wiki/wiki/Ethash)).

To decrease the possibility of 51% attacks, a [stake requirement](https://github.com/Karbovanets/papers/blob/master/PoW%20with%20Stake.md) should be added as well as publicly (to avoid sybil attacks) known [masternodes](https://github.com/Karbovanets/papers/blob/master/Transaction%20cancellation%20in%2051%25-attack.md).
