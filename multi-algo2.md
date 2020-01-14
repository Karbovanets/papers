# Multi-Algo PoW for CryptoNote

Ongoing saga of anti-ASIC movement is unfolding in the scene of CryptoNote coins, which Karbo still lingers to take part in. The reason of trying to avoid ASICs is to avoid centralization and monopolization of mining. The goal is to make mining accessible for everyone, not just for those who can afford the hardware to mine it, i.e. the *egalitarian mining* — one of the corner stones of CryptoNote.

The one possible route that was omitted by all CryptoNotes until now is **multi-algorithm mining**. Instead of choosing one group of miners, why not try to create level field for all of them? Such idea was discussed by Monero developers, but it was rejected because they decided that it doesn't really work. [1] We refer to Monero as based on same CryptoNote protocol and with CryptoNight PoW algorithm as Karbo. However there are several successfully working Multi-Algo non-CryptoNote coins, for example DigiByte, MyriadCoin to mention a few. Inspired by their examples, below we propose and describe our own approach.


## How Does Multi-Algo PoW Work?

Typically Multi-Algo PoW has separate difficulties for each algorithm which are adjusted independently based on the history of block times on those algorithms. But having multiple parallel difficulties makes it easier to game one of them for example by manipulating timestamps as seen on Verge blockchain [2]. If alternating is enforced, the whole network may halt waiting for current algorithm to find a block if it is stuck because of high difficulty or, for example, the lack of miners on this algorithm, etc.

In first version of this KIP we proposed to use single **base difficulty**, common for all algorithms, that is used to calculate the amount of work for longest chain. In that proposal each algorithm had its own **algo work factor** that allowed scaling the difficulty for given algorithm depending on its hardware productivity. The individual **algo difficulty** were calculated as **base difficulty** multiplied by **algo work factor**. But it is impossible to correctly define this work factor. 

Therefore, we have to select parallel approach, where we use **separate difficulties** for each algo and during the reorganization compare each algo's chain work separately. **Reorganization to alternative chain happens only if all algos on than chain have biggest cumulative difficulty.**

To protect from excessive amount of hashrate for a single algorithm and to prevent mining the large sequence of blocks on the same algorithm, a soft limit of how many blocks can be mined in sequence is imposed: for individual block difficulty calculation previous blocks are iterated backwards. If iterated (previous) block has the same algo as the currently mined, the **algo difficulty** for currently mined block is doubled. This repeats until we hit the block on different algo. This way the difficulty for sequence of blocks with the same algo is growing in geometric progression for every block with the same algo, eventually forcing a delay allowing miners on other algorithms to mine blocks. What's more, only individual **algo difficulty** is raising quickly in case of any particular algorithm is getting hashrate boost in the first place.

The implementation is following: instead of single `cumulative_difficulty` of the `BlockEntry`, separate `algo_cumulative_difficulty` for each algo is stored in the blockchain database. When new block is added to the database, the current difficulty is appended to each `algo_cumulative_difficulty`, bein zero for other algos than currently mined. This way for other algos their cumulative difficulty remains unchanged, only current algo cumulative difficulty is increased.

Three algorithms were initially planned for use in new Multi-Algo PoW: one for ASICs, one for GPUs and one for CPUs. We were going to retain the original CryptoNight algo for ASICs since they are already available, but because block template is modified with new `algo` field introduced they are no longer compatible. 

For GPU mining we are going to use CN-GPU algo by Ryo Currency [3] as "fair GPU mining". For CPU we are going to use the variant of CryptoNight CN-POWER (Cryptonight-KRB), which is unfriendly to GPUs and neutral to ASICs and FPGAs.

Having just two algos, we can use tuple to store two cumulative difficulties in the database, or vector for more difficulties.

## References

[1] https://www.reddit.com/r/Monero/comments/c1t4zu/are_multiple_mining_algorithms_possible_with/

[2] https://blog.theabacus.io/the-verge-hack-explained-7942f63a3017

[3] https://medium.com/@crypto_ryo/cryptonight-gpu-fpga-proof-pow-algorithm-based-on-floating-point-instructions-92524debf8e8


