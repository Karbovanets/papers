# POWS2 - addendum to PoW with Stake in Karbo
Author: [Aiwe](https://github.com/aivve/)

In the first version of “PoW with Stake” proposal for Karbo [1] it was proposed to add a stake to POW in the following manner: “... if a miner wants to contribute his all hash power to the network (suppose *p* percent of all hash power of the network), the miner must stake the number of tokens that is proportional to *p*.” We simplified this approach by using network-wide *difficulty* instead of individual hashpower, multiplied by a *stake to difficulty ratio*, which is called *m* factor: “In order to mine a block the miner must stake the number of coins that is not less than the current minimum amount which is determined by the *difficulty*. The preliminary proposal is that the minimum *stake* in atomic units should be equal to the next difficulty multiplied by factor *m*. This factor should be defined economically from the current network state and conditions.” [1] How exactly this factor should be defined was not explained in that proposal. The problem is that *m* factor or a *stake to difficulty ratio* in the first proposal is arbitrary and hard to be set properly. In this article, we try to make it not arbitrary and to find a way of determining it.

In the proposed setup it is the variative *difficulty*, that regulates the profitability of *stake deposits*, as *stake* can be described as a *short term deposit*. And deposit has to yield returns or *interest*. In other words, locking amount of coins `S` for the whole day `L` to get reward `R` must be economically profitable. **It is extremely important to select the *base stake* level that will be attractive to miners.** As we stated above, miners themselves define the level of stakes by the intensity of mining i.e. *difficulty* which is in turn, related to coin’s price. Long cooldown period `L`, the period for which coins included in stake are locked, makes it harder for single party to achieve mining centralization by mining all blocks in a row because this party would also need the significant percent of coins in circulation to be able to do so (remember that for each of 360 blocks of cooldown `L` it is necessary to have separate amount for *stake*). In the same time, the risk of the insufficient number of active miners with enough coins for stakes to mine `L` blocks until some of the locked coins will unlock allowing miners to repeat the cycle should be avoided. Incorrectly selected *stake to difficulty ratio* constant will make mining with *stake deposits* unprofitable and unattractive, potentially leading to the spiral of death and blockchain getting stuck, especially if miners will be able to drive difficulty up, and in consequence, stake will become too high for any participant. Therefore the second part of the equation, the *stake to difficulty ratio* should be defined in such a manner that it will ensure the profitability of *stake deposits*. The *interest rate* of *stake deposit* then can be determined as:

`I = R × 100 ÷ So`

where `I` is the interest rate, `R` is the current reward, `So` is the required stake, optimal for profitability. The desired interest rate will allow us to check that selected *stake to difficulty ratio* satisfies the requirement of interest rate to be within attractive limits:

`So = R ÷ I × 100`

or

`So = R × 100 ÷ I`.

Reversing the initial stake definition 

`S = D × M`

by substituting current difficulty in it with average historical data we get ratio:

`M = So ÷ Da`

where `Da` is an average (historical) difficulty, `So` is the optimal stake for interest `I`. 

From historical data we can use average difficulty `Da` and average reward `Ra` to determine the profitable trailing ratio:

`M = (Ra × 100 ÷ I) ÷ Da`.

Then active stake is:

`S = D × (Ra × 100 ÷ I) ÷ Da`.

This is one possible way of *stake* calculation based on profitability, which has only one arbitrary element, — the desired profitability or *interest rate* `I` which is still better than completely random factor `m`. The benefit and improvement here is that this setup will be self-adjusting by average difficulty and average reward which are changing over time.

But apart from profitability, our concern is coin’s security. We need to select such stake level which will ensure that significant percent (at least 25%) of all emitted coins will be constantly engaged in mining and securing the chain. Obtaining large percent of coins in circulation in order to monopolize mining will be prohibitive, the stake level in this approach will also be quite high, thus making 51% attack attempts more costly. Besides, it can take days before miner actually finds block and coins are actually locked but he obviously can not spend those coins while he is mining. So those coins are busy, although not technically locked during the mining, which means that the percent of coins actually engaged in mining is not only the number of currently locked coins. 

Let emitted coins is `E`, then *stake* `S` can be determined from total emission divided by lock period `L` of *stake deposit*, divided by `P`, which is the part of emission:

`So = E ÷ L ÷ P`.

We can adjust the percent of engaged coins `P` in the setup to achieve *stake* `S` within profitability range if needed, provided we know what interest rate (profitability) is considered as minimum acceptable. `P` for 25% of emission is 4.
 
We also have to remember that the reward is diminishing over time according to the emission curve, and this has to be taken into account to retain profitability and attractiveness as well. Therefore reward must be included in adjusted stake calculation:

`So = E ÷ L ÷ P ÷ Ri × R`

where `R` is the current reward and `Ri` is the initial reward on the time when the POWS is activated because for this reward we tweak initial parameters (actually we tweak `P` as an only tweakable parameter). Initial reward `Ri` can be replaced by historical average reward `Ra`. However, instead of the average reward for the entire history in order to get correct desired percent of engaged coins and required *stake* we have to use the average reward for the new POWS epoch. Alternatively, we can tweak profitability by selecting appropriate `P` for reward which is current at the beginning of new POW epoch, so the final *stake* will engage wanted percent of coins in circulation after it is changed by the adjustment based on the average reward for the entire history.

Now, after we determined optimal stake `So` for coin security and profitability, using historic average difficulty `Da`, current difficulty `D`, we can find a stake to difficulty ratio. The proportion between them is the ratio we need:

 `M = Da ÷ So = D ÷ S`.

Perhaps *average difficulty* `Da` should be replaced with *median difficulty* `Dm` to exclude outliers, but this will cause serious overhead in current implementation. Now we store cumulative difficulties and can calculate average difficulty easily just by dividing current cumulative difficulty by the current height (or use appropriate height and cumulative difficulty as required). But to calculate median difficulty we have to create, store and constantly refresh a vector of individual difficulties because doing this every time it is needed in runtime is too heavy.

New POW is a new Epoch, the historic average difficulty then should be calculated starting from the beginning of new Epoch (activation of the new POW) to exclude the influence of ASICs. Having optimal stake determined from emission and reward correlation, described above, as `So = E ÷ L ÷ P ÷ Ri × R`, where `Ri` is an initial reward on epoch start, and epoch average difficulty `Da`, we can determine *active stake* that is adjusted by current difficulty vs epoch average difficulty:

`S = D × So ÷ Da`

or

`S = D × (E ÷ L ÷ P ÷ Ri × R) ÷ Da`. (1)

This is the better approach for a *stake* calculation, where we also have one arbitrary element — the percent of engaged coins. Nevertheless, this parameter is not completely random as we select the desired percent of engaged emission based on observation of the same percent in other coins. 

Making parallels to the profitability based stake definition and because initial reward may be, or even should be replaced with the average reward we can say that

`S = D × (Ra ÷ I × 100) ÷ Da`

is the same as

`S = D × (E ÷ L ÷ P × R ÷ Ra) ÷ Da`

hence 

`Ra ÷ I × 100 = E ÷ L ÷ P × R ÷ Ra`

then

`I = Ra × 100 ÷ E ÷ L ÷ P × R ÷ Ra`

where only P is at our disposal to adjust. Thus we have replaced arbitrary *interest rate* by security-based one and now have our *stake* calculation formula:

`S = D × (Ra ÷ Ra × 100 ÷ E ÷ L ÷ P × R ÷ Ra × 100) ÷ Da`

which after removing dividing out elements gives us the same

`S = D × (E ÷ L ÷ P × R ÷ Ra) ÷ Da` (2)

as (1) if we replace initial reward `Ri` by average reward `Ra`, thus (1) or (2), which are the same, is the final formula for *stake* calculation.

We can also set maximum and minimum limits for safety. To simplify the implementation we can safely set the upper limit as 10 000 KRB and minimum limit set as `100 × R` (which gives 1% daily interest rate), or even just 100 KRB, using tail emission reward as reference.


### References
[1] https://github.com/Karbovanets/papers/blob/master/PoW%20with%20Stake.md





