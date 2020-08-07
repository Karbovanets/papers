# POWS — PoW with Stake in Karbo


## What is wrong with POW and why we are going to change it


The security of PoW is based on the assumption that it is unfeasible to achieve the prevail in a hash rate for a single entity and even if such entity will possess that hashrate it will be economically motivated not to attack network due to its investments in mining infrastructure, which is not true, especially for small coins.

The major concern for small PoW based cryptocurrencies recently has become the availability of sheer amount of hashrate that is not their native but is available for rent. This results in a series of attacks on coins utilizing rented hashrate. There is even the website *crypto51.app* which collects the theoretical cost of a 51% attack on various networks. As Scott Roberts (aka Zawy) wrote, “...the only thing protecting PoW is the stake of the equipment infrastructure... All the small coins switching to PoW algorithms that can't be easily rented is an attempt to make miners hold an equipment stake." “This shows that work in PoW is not equal to security, and secure part of PoW is PoS. If Bitcoin hashrate was rentable (no mining stakeholders) Bitcoin double spends would be easy enough to make it worthless”. He continues, “In Monero's case, PoW change was not to reduce NiceHash renting (the reason small coins change PoW) but to reduce the effects of ASICs that were in a few hands. So the key idea in both renting and concentrated ASIC problems, is that PoW works by having distributed equipment owners (stakes). It has nothing to do with work (waste). Value is created by work (waste) in BTC, which can't be done in PoS. But securing established value is accomplished by risk of value, not waste. When buying equipment, you are locking up a stake just like PoS systems require...” [1]

In Coinbase's blog post Mark Nesbitt expresses the same idea: "**It is a security feature for a particular coin’s mining operations to be the dominant application of the hardware used to mine that coin.** ... *Owners of the hardware lose the value of their investment if the primary application of the hardware loses value.* ... Hardware owners are incentivized to consider the long term success of the main application of their hardware. The longer the lifetime of their equipment, the more invested they become in the long-term success of the hardware’s primary application. At time of writing, Bitcoin ASICs are beginning to have significantly longer useful lifespans as efficiency increases of newer models are diminishing." [2]

And that "*Large pools of computational power that exist outside of a coin pose a security threat to the coin.* ... Coins at the greatest risk of 51% attack are the ones where there exists large amounts of hashpower not actively mining the coin that could begin mining and disrupt the coin’s blockchain. This is especially important to consider in light of the argument above regarding hardware owners’ incentives regarding their hardware’s application — if the owners of the hardware have other applications outside of mining where they can monetize their hardware investment, the negative consequences of disrupting a coin’s blockchain are minimal. ... Algorithm changes to “brick ASICs” simply allow the massive general purpose computational resources of the entire world to mine, and potentially disrupt, a cryptocurrency at will. Coins that have implemented “ASIC-resistant” algorithms have been, empirically, very susceptible to 51% attacks for this very reason. Notable examples of ASIC-resistant coins that have been successfully 51% attacked include BTG, VTC, and XVG. To date, there is not a single case where a coin that dominates its hardware class has been subject to a 51% double spend attack." [2]

They summarize: "The only way a proof-of-work coin can materially reduce the risk from 51% attacks is to be the dominant application of the hardware used to mine the asset. A coin mined on widely available general purpose hardware, such as CPUs and GPUs, lacks this major security feature." [2]

Being a small coin, we can not expect that upon adopting of an ASIC-friendly algorithm a dedicated hardware ASIC will be developed specially and exclusively for Karbo. The existing ASICs for CryptoNight algorithm are not exclusive to Karbo.

Dominating the particular POW algorithm is not enough if there is massive hashrate available to switch to Karbo at any time (coming from any other GPU/CPU algorithm if we change the algorithm, or from ASICs that are not currently mining Karbo but other compatible coins). In other words, merely changing POW algo to our own unique and exclusive will not remove the threat and will not improve the security of the Karbo network. On the contrary, this can potentially do more harm than good, if we take into account that there are no many coins available for mining with existing CryptoNight ASICs left.

Above we identified a problem in the current state of PoW — the lack of security ensured by, as we call it, *a stake in equipment*.


## The proposed solution

The obvious, naive and simple solution is to add to PoW, what has become missing — a **stake**. But **instead of enforcing a stake in a hardware equipment we are going to enforce a stake in a coin directly**. This approach can be named "Membership POW" (MPOW), as only members or coin holders can mine it. Alongside we will change POW algorithm to CPU-mineable, GPU-unfriendly and ASICs/FPGA- neutral. We anticipate that POW part will become supplimentary, it will be mieable by solo miners with stakes in coin on their home PCs. Hovewer, this does not exclude the possibility of emerging of the mining pools.

\
The basic idea is that in order to mine a block the miner must stake the number of coins that is not less than the current minimum amount. It should be mentioned that the similar idea was first proposed by Qi Zhou [3], however our approach is different.

A miner forms the coinbase transaction as follows: he sends to himself (or someone else, unimportant) the amount not less than the required minimum and adds the block reward. Coins transferred in a coinbase transaction prove possession without revealing sender and recipient. This is enough to prove and verify his collateral stake in a simple way. There is *mined money unlock window* `L`, a rule which locks all outputs in coinbase transaction for a period of *n* blocks. This means that coins from coinbase transaction can be spent only after *n* blocks are mined. Therefore, to be able to mine blocks successively, miner will have to possess much more money than minimum stake amount for one block, — he will need a separate stake for each block until his stake for his first mined block is unlocked. This will substantially increase the cost of 51% attack, the cost of being large miner or running a mining pool since the miner or the operator of the pool will have to acquire sufficient part of coins in circulation. [4]

The minimum *stake* should be based on economical security of the network and profitability of stake deposits. A *stake* can be described as a **short term deposit**, and deposit has to yield returns or *interest*. In other words, locking amount of coins `S` for the whole day `L` to get reward `R` must be economically profitable. The *interest rate* `I` of *stake deposit* can be determined as:

`I = R × 100 ÷ Si`,

where `R` is the current reward, `Si` is the required stake, optimal for profitability. Lets call this profitability- or interest-driven stake `Si`. The desired interest rate will allow us to find *stake* which satisfies the requirement of interest rate to be within attractive limits:

`Si = R ÷ I × 100 = R × 100 ÷ I`.

Too high *minimum stake* will make mining with *stake deposits* unprofitable and unattractive, potentially leading to the spiral of death and blockchain getting stuck. Therefore the *minimum stake* should be defined in such a manner that it will ensure the profitability of *stake deposits* and attactivenes of mining. We also have to remember that the reward is diminishing over time according to the emission curve, and this has to be taken into account to retain profitability and attractiveness as well.

But apart from profitability, our concern is coin’s security. We need to select such stake level which will ensure that significant percent (at least 25%) of all emitted coins will be constantly engaged in mining and securing the chain. Obtaining large percent of coins in circulation in order to monopolize mining will be prohibitive, the stake level in this approach will also be quite high, thus making 51% attack attempts more costly. Besides, the percent of coins actually engaged in mining is not only the number of currently locked coins: it can take days before miner actually finds block and coins are actually locked, but he can not spend those coins while he is mining. We can say that the coins are busy, although not technically locked during the mining.

Let emitted coins is `E`, then emission-based *stake* `Se` can be determined from total emission divided by lock period `L` of *stake deposit*, divided by `P`, which is the part of total number of coins in circulation, targeted to be engaged in mining (for 25% of emission `P` = 4):

`Se = E ÷ L ÷ P`.

Finally we combine the profitability-driven stake `Si` and emission-driven stake `Se` into golden mean and get our `base stake`:

`S = (Si + Se) ÷ 2`

or

`S = (R ÷ I × 100 + E ÷ L ÷ P) ÷ 2`.

The part `I ÷ 100` can be rounded to 666, because interest rate per day is 0.15 (calculated by emission-based stake and reward):

`S = (R × 666 + E ÷ L ÷ P) ÷ 2`.

This is the base value of the collateral stake for a lock period of `L` blocks.


### Variable stake, term and difficulty

There still is a risk that there may be too few active miners with enough coins to lock in the collateral stakes after combining two stake levels (profitability-drived and emission-base), causing the network to stop. The cooldown period `L` creates the risk of the insufficient number of active miners with enough coins for stakes to mine `L` blocks continuously until some of the locked coins will unlock allowing miners to repeat the cycle, which should be avoided. In order to prevent such scenario, the following rules are proposed:

1)  A *variable amount* of collateral stake deposit is allowed, but, the **term** of the deposit then changes proportionally, according to the following formula:

    `T = Sb ÷ Sa × Tb`,

    where `T` is the *actual deposit term* of the miner, `Tb` is the *base deposit term* (used to calculate base stake amount), `Sb` is the base stake deposit amount, `Sa` is the actual stake deposit amount.

2)  The actual *difficulty* of a block mined with less stake `Sa` than the *base stake* `Sb` should be proportionally greater than the *base difficulty* `Db`:

    `D = Sb ÷ Sa × Db`.

    Base difficulty is used in diffculty adjustment calculations, whereas actual difficulty of the block is checked against the actual stake.

3)  When reorganizing, the alternative chain should have not only more work (greater cumulative difficulty) but also greater cumulative stake (the sum of all stakes in blocks).

4)  Maximum and minimum deposit term limits are introduced:

    * the minimum term is 10 blocks;
    * the maximum term is ‭131400‬ blocks (360 × 365) or about one year.

The first rule will allow smaller stake deposits to be locked, but for a longer period thus stimulating a larger stakes.

The second rule also encourages mining with a larger collateral stake, as the difficulty for the miner is much lower in this case.

The third rule enhances the protection against 51%-attacks using only the relatively large mining power without significant deposits of collateral stakes. Generally, as large stakes are stimulated, additional obstacles are created for 51% attacks, since we anticipate that reorganization into a certain number of blocks will require a rather large amount of coins for cumulative stake.

The minimum term for deposit creates the maximum effective stake amount which can be calculated by current reward and emission. 
    
In fact this effective stake amount is the same as the *base stake* because it is calculated using the minimum term as the *base term* in our implementation.

The maximum term of stake deposit means that there will be a minimum stake amount, which can be calculated from that term.

The reward for mined blocks with a small stake will be locked for a long time together with stake, which also diminishes the attractiveness of such mining, leaving it as an emergency measure.


## References:

[1] https://twitter.com/zawy3/status/1082199522812612608

[2] https://blog.coinbase.com/how-coinbase-views-proof-of-work-security-f4ba1a139da0

[3] https://medium.com/quarkchain-official/proof-of-staked-work-ef36f9499279

[4] https://github.com/Karbovanets/papers/blob/master/POWS.md

[5] https://github.com/Karbovanets/papers/blob/master/POWS2.md

