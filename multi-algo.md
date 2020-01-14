# Multi-Algo PoW for CryptoNote

Ongoing saga of anti-ASIC movement is unfolding in the scene of CryptoNote coins, which Karbo still lingers to take part in. The reason of trying to avoid ASICs or even GPUs is to avoid centralization and monopolization of mining. The goal is to make mining accessible for everyone, not just for those who can afford the hardware to mine it, i.e. the *egalitarian mining* — one of the corner stones of CryptoNote.

The one possible route that was omitted by all CryptoNotes until now is **multi-algorithm mining**. Instead of choosing one group of miners, why not try to create level field for all of them? Such idea was discussed by Monero developers, but it was rejected because they decided that it doesn't really work. [1] However there are several successfully working Multi-Algo non-CryptoNote coins, for example DigiByte, MyriadCoin to mention a few. Inspired by their examples, below we propose and describe our own approach.


## How Does Multi-Algo PoW Work?

Typically Multi-Algo PoW has separate difficulties for each algorithm which are adjusted independently based on the history of block times on those algorithms. It was to protect from excessive amount of hashrate for a single algorithm. But having multiple parallel difficulties makes it easier to game one of them for example by manipulating timestamps as seen on Verge blockchain [2]. If alternating is enforced, the whole network may halt waiting for current algorithm to find a block if it is stuck because of high difficulty or, for example, the lack of miners on this algorithm, etc.


We use single **base difficulty**, that is common for all algorithms and is used to calculate the amount of work for longest chain. Each algorithm has its own **algo work factor** that allows scaling the difficulty for given algorithm depending on its hardware productivity. The individual **algo difficulty** is calculated as **base difficulty** multiplied by **algo work factor** in the first step.

In the second step, we use algo difficulty balancer which works like this: to prevent mining the large sequence of blocks on the same algorithm, a window of *n* last blocks is iterated. If block has the same algo as the currently mined, the **algo difficulty** for block template is doubled. If the algo is different, the **algo difficulty** is divided by square root of 2.

This way the difficulty for sequence of blocks with the same algo is growing in geometric progression for every block with the same algo, eventually forcing a delay allowing miners on other algorithms to mine blocks. Moreover, the difficulty for other algos decreases proportionally, which makes mining a block easier for them. What's more, the individual **algo difficulty** is raising quickly in case of any particular algorithm is getting hashrate boost in the first place instead of common **base difficulty**. If each algorithm has 33% of blocks, the difficulties for each algo are balanced in equilibrium.


### Choosing Algos

Three algorithms will be used in new Multi-Algo PoW: one for ASICs, one for GPUs and one for CPUs. We are going to retain the original CryptoNight algo for ASICs since they are already available and can be considered as widespread in the wild. For GPU mining we are going to use CN-GPU algo by Ryo Currency [3]. For CPU we are going to use the variant of CryptoNight CN-POWER (Cryptonight-KRB), which is unfriendly to GPUs and neutral to ASICs and FPGAs.


### Choosing Algo Work Factor

The weak point and crucial element in this setup is **algo work factor** which has to be correctly and carefully selected.

 At our disposal there is such information:

* CryptoNote ASICs are 145X more efficient than CPU (for CryptoNight). [4] 

* Antmain X3 ASIC's hashrate is 220 Kh/s on CryptoNight, whereas average CPU has up to 1Kh/s on Karbo's CN-POWER algo, average GPU at 1Kh/s on CN-GPU.

* Comparing average difficulty for a period before the incursion of ASICs and after they switched to Karbo, we have 40x increase of average difficulty.

Based on the above, our guesstimated work factor for CryptoNight algorithm is 256.


## References

[1] https://www.reddit.com/r/Monero/comments/c1t4zu/are_multiple_mining_algorithms_possible_with/

[2] https://blog.theabacus.io/the-verge-hack-explained-7942f63a3017

[3] https://medium.com/@crypto_ryo/cryptonight-gpu-fpga-proof-pow-algorithm-based-on-floating-point-instructions-92524debf8e8

[4] https://www.reddit.com/r/Monero/comments/aovypq/randomx_asic_resistant_pow_community_feedback/

