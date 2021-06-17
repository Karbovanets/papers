# Signed Proof-of-Work

One of the key principles of CryptoNote is **Egalitarian Proof of Work**: "The proof of work mechanism acts as a voting system. Thus, it is crucial that during the voting process all the participants have equal voting privileges." [1] The standard POW algorithm for CryptoNote coins named CryptoNight does not provide not only pooled mining resistance, it is also no longer ASIC resistant. The Karbo cryptocurrency sill uses CryptoNight in spite ASICs were developed for it and the majority of CryptoNote based coins that were using it migrated to its variants with modifications of different magnitudes under the leadership of Monero (and mostly Monero CryptoNight variants were used). At Karbo we came to the conclusion that small tweaks of the hashing algorithm are not sufficient. We think that as soon as it becomes economically profitable, ASICs are developed for any algorithm. Therefore we were researching various approaches to the PoW change. Moreover, this does not prevent pooled mining and hence its centralization. The centralization of mining should be prevented. Below we present some reasons.

It's crucial for blockchain decentralization that as many regular users as possible run a node and are mining solo. If there are only few pools and block explorers they can perform coordinated attack, decide which blockchain fork is "right", they can censor transactions or roll back them etc.

The importance of censorship resistance which can be only ensured by a decentazlized mining can be illustrated by the following announce as an example: “Marathon Digital Holdings announced on March 30, 2021 the launching of the first Bitcoin mining pool in North America that is fully compliant with the United States regulations, including anti-money laundering and the Office of Foreign Asset Control's standards. The mining process will include an algorithm that is able to exclude transactions from personas that are named on the Department of Treasury's Specially Designated Nationals and Blocked Persons List". [2]

"The elites of your blockchain community, including pools, block explorers and hosted nodes, are probably quite well-coordinated; quite likely they're all in the same telegram channels and wechat groups. If they really want to organize a sudden change to the protocol rules to further their own interests, then they probably can." - desribes the problem Vitalik Buterin. [3]

"If every user ran a verifying node, then the attack would have quickly failed: a few mining pools and exchanges would have forked off and looked quite foolish in the process. But even if some users ran verifying nodes, the attack would not have led to a clean victory for the attacker; rather, it would have led to chaos, with different users seeing different views of the chain. At the very least, the ensuing market panic and likely persistent chain split would greatly reduce the attackers' profits. The thought of navigating such a protracted conflict would itself deter most attacks." [3]

"If you have a community of 37 node runners and 80000 passive listeners that check signatures and block headers, the attacker wins. If you have a community where everyone runs a node, the attacker loses. We don't know what the exact threshold is at which herd immunity against coordinated attacks kicks in, but there is one thing that's absolutely clear: more nodes good, fewer nodes bad, and we definitely need more than a few dozen or few hundred." [3]

"It is all about the fact that an individual miner that connects to these pools are no longer participating in decision making. Decisions such as which transactions to be included in the block they mine, which chain to follow, which proposal to vote for, or even which coin they are mining!..." [6]

In attempt to disable and prevent centralization of mining we are introducing botnet and mining pools resistant solo mining approach, called “Signed Proof-of-Work”. The main idea behind SPoW is to sign header data with the private key of the miner and that the address in the coinbase transaction corresponds to this key [4]. For CryptoNote additional means are required to verify that the corresponding miner address received the reward. The idea is not novel, the similar approach was described as a "Two-Phase PoW" [5] and was discussed on the Bitcointalk [6].

### How it worsk, exactly?

Basically, to the Block structure is added a new field called `signature` which stores the miner's signature of the block, obviously. The miner signs the block `header` including `nonce` with his `private keys`. Then he hashes the block hashing blob that includes `header` **and** `signature`. So, currently miner hashes `H(<block_header>)`, after the proposed change he will be hashing `H(<block_header><block_header_signature>)`. Because the `nonce` is included in the block hashing blob as a part of the `header`, with every interation changing `nonce` he has to sing the block again, and for this he needs private keys. Pool operator can not perform signing, so this effectively disables pooled mining.

In CryptoNote coins it is not enough just to sign block header, the proof that the miner address corresponding to the keys received the reward is needed. We need to prove that miner knows the `private view key` because it is needed to spend coins together with the `private spend key`. Having only the last one without `private view key`, miner would not be able to steal the reward so pool operator could have issued to miners only the `secret spend key` to sign the block withholding the `private view key`.

One of the ways would be to include also miner's `public address` and his `private view key`, which would allow to verify that he did receive the reward. However, this approach requires to store in the block 3 fields: miner's public address, miner's secret view key and miner's block signature.

The alternative would be to derive an ephemeral secret key from miner's both `secret view key` and `secret spend key` together with miner transaction's data (its public key and one of the output's target public keys) and sing the block with this `derived key`. The number of the output is determined by the `nonce`. Then the signature is validated against the determined output's target public key of the miner transaction. This covers the proof of the receiving the reward as well. In this approach only the `signature` is stored in the block. It retains miner's privacy as no his public address nor private view key are published.


    
### References

[1] http://web.archive.org/web/20200614004825/https://cryptonote.org/inside#equal-proof-of-work

[2] https://ir.marathondh.com/news-events/press-releases/detail/1239/marathon-digital-holdings-becomes-the-first-north-american

[3] https://vitalik.ca/general/2021/05/23/scaling.html

[4] https://github.com/volbil/spow/

[5] https://hackingdistributed.com/2014/06/18/how-to-disincentivize-large-bitcoin-mining-pools/

[6] https://bitcointalk.org/index.php?topic=5254327.0


