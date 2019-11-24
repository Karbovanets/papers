# POWS — PoW with Stake in Karbo

The security of PoW is based on the assumption that it is unfeasible to achieve the prevail in a hash rate for a single entity and even if such entity will possess that hashrate it will be economically motivated not to attack network due to its investments in mining infrastructure, which is no longer true. The major concern for small PoW based cryptocurrencies recently has become the availability of sheer amount of hashrate that is not their native but is available for rent. This results in a series of attacks on coins utilizing rented hashrate. There is even the website crypto51.app which collects the theoretical cost of a 51% attack on various networks. As Scott Roberts (aka Zawy) wrote, “...the only thing protecting PoW is the stake of the equipment infrastructure... All the small coins switching to PoW algorithms that can't be easily rented is an attempt to make miners hold an equipment stake." “This shows that work in PoW is not equal to security, and secure part of PoW is PoS. If Bitcoin hashrate was rentable (no mining stakeholders) Bitcoin double spends would be easy enough to make it worthless”. He continues, “In Monero's case, PoW change was not to reduce NiceHash renting (the reason small coins change PoW) but to reduce the effects of ASICs that were in a few hands. So the key idea in both renting and concentrated ASIC problems, is that PoW works by having distributed equipment owners (stakes). It has nothing to do with work (waste). Value is created by work (waste) in BTC, which can't be done in PoS. But securing established value is accomplished by risk of value, not waste. When buying equipment, you are locking up a stake just like PoS systems require...” [1]

In Coinbase's blog post Mark Nesbitt states the same: "**It is a security feature for a particular coin’s mining operations to be the dominant application of the hardware used to mine that coin.** ... *Owners of the hardware lose the value of their investment if the primary application of the hardware loses value.* ... Hardware owners are incentivized to consider the long term success of the main application of their hardware. The longer the lifetime of their equipment, the more invested they become in the long-term success of the hardware’s primary application. At time of writing, Bitcoin ASICs are beginning to have significantly longer useful lifespans as efficiency increases of newer models are diminishing." [2]

And that "*Large pools of computational power that exist outside of a coin pose a security threat to the coin.* ... Coins at the greatest risk of 51% attack are the ones where there exists large amounts of hashpower not actively mining the coin that could begin mining and disrupt the coin’s blockchain. This is especially important to consider in light of the argument above regarding hardware owners’ incentives regarding their hardware’s application — if the owners of the hardware have other applications outside of mining where they can monetize their hardware investment, the negative consequences of disrupting a coin’s blockchain are minimal. ... Algorithm changes to “brick ASICs” simply allow the massive general purpose computational resources of the entire world to mine, and potentially disrupt, a cryptocurrency at will. Coins that have implemented “ASIC-resistant” algorithms have been, empirically, very susceptible to 51% attacks for this very reason. Notable examples of ASIC-resistant coins that have been successfully 51% attacked include BTG, VTC, and XVG. To date, there is not a single case where a coin that dominates its hardware class has been subject to a 51% double spend attack." [2]

They summarize this claim: "The only way a proof-of-work coin can materially reduce the risk from 51% attacks is to be the dominant application of the hardware used to mine the asset. A coin mined on widely available general purpose hardware, such as CPUs and GPUs, lacks this major security feature." [2]

Thus we identified a problem in current state of PoW — the lack of security ensured by a *stake in equipment*.

Being a small coin, we can not expect that upon adopting of ASIC-friendly algorithm a dedicated hardware ASIC will be developed specially and exclusively for Karbo. The existing ASICs for CryptoNight algorithm are not dedicated to Karbo. 

We agree with Coinbase that dominating of the particular POW algorithm is not enough if there is massive hashrate available to switch to Karbo at any time (coming from GPU/CPU or ASICs that are not currently mine Karbo but other coins). I.e. merely changing POW algo to own unique one will not solve the threat and will not improve the security of Karbo network.

So we came to perhaps obvious, naive and simple solution: add to PoW, what has become missing — a **stake**. But **instead of enforcing a stake in hardware equipment we decided to enforce a stake in coin directly**. This approach can be named "Membership POW" MPOW, as only members or coin holders can mine it. Alongside we will change POW algorithm to CPU-mineable, GPU-unfriendly and ASICs/FPGA- neutral. We anticipate that POW part will become supplimentary, it will be mieable by solo miners with stakes in coin on their home PCs.

## How it works?

The basic idea is that in order to mine a block the miner must stake the number of coins that is not less than the current minimum amount. It should be mentioned that the similar idea was first proposed by Qi Zhou [3], however our approach is different.

A miner forms the coinbase transaction as follows: he sends to himself (or someone else, unimportant) the amount not less than the required minimum and adds the block reward. Coins transferred in a coinbase transaction prove possession without revealing sender and recipient. This is enough to prove and verify his collateral stake in a simple way. There is *mined money unlock window* `L`, a rule which locks all outputs in coinbase transaction for a period of *n* blocks. This means that coins from coinbase transaction can be spent only after *n* blocks are mined. Therefore, to be able to mine blocks successively, miner will have to possess much more money than minimum stake amount for one block, — he will need a separate stake for each block until his stake for his first mined block is unlocked. This will substantially increase the cost of 51% attack, the cost of being large miner or running a mining pool since the miner or the operator of the pool will have to acquire sufficient part of coins in circulation. [4]

In the first version of the KIP-002 the minimum stake amount was determined by difficulty. The preliminary proposal was that the minimum *stake* in atomic units should be equal to the next difficulty multiplied by factor *m*. Miners themselves define the level of stakes by the intensity of mining i.e. *difficulty* which is in turn, related to coin’s price. This `m` factor should be defined economically from the current network state and conditions.” [4] How exactly this factor should be defined was not explained in that proposal. Moreover, the problem is that *m* factor or a *stake to difficulty ratio* is arbitrary and hard to set properly. 

While we were trying to find a way of determining the above difficulty factor [5] we came to this alternative proposal, that is based on economical security of the network and profitability of stake deposits not on difficulty. A *stake* can be described as a **short term deposit**, and deposit has to yield returns or *interest*. In other words, locking amount of coins `S` for the whole day `L` to get reward `R` must be economically profitable. The *interest rate* `I` of *stake deposit* can be determined as:

`I = R × 100 ÷ Si`,

where `R` is the current reward, `Si` is the required stake, optimal for profitability. Lets call this profitability- or interest-driven stake `Si`. The desired interest rate will allow us to find *stake* which satisfies the requirement of interest rate to be within attractive limits:

`Si = R ÷ I × 100 = R × 100 ÷ I`.

**It is important to select the *stake* level that will be attractive to miners** because long cooldown period `L`, however necessary for security, creates the risk of the insufficient number of active miners with enough coins for stakes to mine `L` blocks until some of the locked coins will unlock allowing miners to repeat the cycle, which should be avoided. Incorrectly selected (too high) *minimum stake* will make mining with *stake deposits* unprofitable and unattractive, potentially leading to the spiral of death and blockchain getting stuck. Therefore the *minimum stake* should be defined in such a manner that it will ensure the profitability of *stake deposits* and attactivenes of mining.

But apart from profitability, our concern is coin’s security. We need to select such stake level which will ensure that significant percent (at least 25%) of all emitted coins will be constantly engaged in mining and securing the chain. Obtaining large percent of coins in circulation in order to monopolize mining will be prohibitive, the stake level in this approach will also be quite high, thus making 51% attack attempts more costly. Besides, it can take days before miner actually finds block and coins are actually locked but miner obviously can not spend those coins while he is mining. So those coins are busy, although not technically locked during the mining, which means that the percent of coins actually engaged in mining is not only the number of currently locked coins.

Let emitted coins is `E`, then emission-based *stake* `Se` can be determined from total emission divided by lock period `L` of *stake deposit*, divided by `P`, which is the part of total number of coins in circulation (for 25% of emission `P` = 4):

`Se = E ÷ L ÷ P`.

We also have to remember that the reward is diminishing over time according to the emission curve, and this has to be taken into account to retain profitability and attractiveness as well. To address this we combine the profitability-driven stake `Si` and emission-driven stake `Se` into golden mean:

`S = (Si + Se) ÷ 2`

or

`S = (R ÷ I × 100 + E ÷ L ÷ P) ÷ 2`.

Our `I ÷ 100` can be rounded to 666, because interest rate per day is 0.15 (calculated by emission-based stake and reward, current for the time when this model is introduced to the network), then

`S = (R × 666 + E ÷ L ÷ P) ÷ 2`.

\
\
References:

[1] https://twitter.com/zawy3/status/1082199522812612608

[2] https://blog.coinbase.com/how-coinbase-views-proof-of-work-security-f4ba1a139da0

[3] https://medium.com/quarkchain-official/proof-of-staked-work-ef36f9499279

[4] https://github.com/Karbovanets/papers/blob/master/POWS.md

[5] https://github.com/Karbovanets/papers/blob/master/POWS2.md

