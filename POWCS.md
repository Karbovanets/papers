
# Proof of Work with Cold Stake

v. 1.3


## What is wrong with POW and why we are going to change it?

The security of PoW is based on the assumption that it is unfeasible to achieve the prevail in a hash rate for a single entity and even if such entity will possess that hashrate it will be economically motivated not to attack network due to its investments in mining infrastructure, which is not true, especially for small coins.

The major concern for small PoW based cryptocurrencies recently has become the availability of sheer amount of hashrate that is not their native but is available for rent. This results in a series of attacks on coins utilizing rented hashrate. There is even the website *crypto51.app* which collects the theoretical cost of a 51% attack on various networks. As Scott Roberts (aka Zawy) wrote, “...the only thing protecting PoW is the stake of the equipment infrastructure... All the small coins switching to PoW algorithms that can't be easily rented is an attempt to make miners hold an equipment stake." “This shows that work in PoW is not equal to security, and secure part of PoW is PoS. If Bitcoin hashrate was rentable (no mining stakeholders) Bitcoin double spends would be easy enough to make it worthless”. He continues, “In Monero's case, PoW change was not to reduce NiceHash renting (the reason small coins change PoW) but to reduce the effects of ASICs that were in a few hands. So the key idea in both renting and concentrated ASIC problems, is that PoW works by having distributed equipment owners (stakes). It has nothing to do with work (waste). Value is created by work (waste) in BTC, which can't be done in PoS. But securing established value is accomplished by risk of value, not waste. When buying equipment, you are locking up a stake just like PoS systems require...” [1]

In Coinbase's blog post Mark Nesbitt expresses the same idea: "**It is a security feature for a particular coin’s mining operations to be the dominant application of the hardware used to mine that coin.** ... *Owners of the hardware lose the value of their investment if the primary application of the hardware loses value.* ... Hardware owners are incentivized to consider the long term success of the main application of their hardware. The longer the lifetime of their equipment, the more invested they become in the long-term success of the hardware’s primary application. At time of writing, Bitcoin ASICs are beginning to have significantly longer useful lifespans as efficiency increases of newer models are diminishing." [2]

And that "*Large pools of computational power that exist outside of a coin pose a security threat to the coin.* ... Coins at the greatest risk of 51% attack are the ones where there exists large amounts of hashpower not actively mining the coin that could begin mining and disrupt the coin’s blockchain. This is especially important to consider in light of the argument above regarding hardware owners’ incentives regarding their hardware’s application — if the owners of the hardware have other applications outside of mining where they can monetize their hardware investment, the negative consequences of disrupting a coin’s blockchain are minimal. ... Algorithm changes to “brick ASICs” simply allow the massive general purpose computational resources of the entire world to mine, and potentially disrupt, a cryptocurrency at will. Coins that have implemented “ASIC-resistant” algorithms have been, empirically, very susceptible to 51% attacks for this very reason. Notable examples of ASIC-resistant coins that have been successfully 51% attacked include BTG, VTC, and XVG. To date, there is not a single case where a coin that dominates its hardware class has been subject to a 51% double spend attack." [2]

They summarize: "The only way a proof-of-work coin can materially reduce the risk from 51% attacks is to be the dominant application of the hardware used to mine the asset. A coin mined on widely available general purpose hardware, such as CPUs and GPUs, lacks this major security feature." [2]

Being a small coin, we can not expect that upon adopting of an ASIC-friendly algorithm a dedicated hardware ASIC will be developed specially and exclusively for Karbo. The existing ASICs for CryptoNight algorithm are not exclusive to Karbo.

Dominating the particular POW algorithm is not enough if there is massive hashrate available to switch to Karbo at any time (coming from any other GPU/CPU algorithm if we change the algorithm, or from ASICs that are not currently mining Karbo but other compatible coins). In other words, merely changing POW algo to our own unique and exclusive will not remove the threat and will not improve the security of the Karbo network. On the contrary, this can potentially do more harm than good, if we take into account that there are no many coins available for mining with existing CryptoNight ASICs left.

Above we identified a problem in the current state of PoW — the lack of security ensured by, as we call it, *a stake in equipment*.


## The proposed solution

The obvious, naive and simple solution is to add to PoW, what has become missing — a **stake**. But **instead of enforcing a stake in a hardware equipment we are going to enforce a stake in a coin directly**. This approach can be named "Membership POW" (MPOW), as only members or coin holders can mine it. Alongside we will change POW algorithm to CPU-mineable, GPU-unfriendly and ASICs/FPGA- neutral (at least if we will not be able to think off ASIC- resistant algo as well). We anticipate that POW part will become more supplimentary, the coin will be mieable by solo miners on their home PCs. Hovewer, this does not exclude the possibility of emerging of the mining pools completely.

\
The basic idea is that in order to mine a block the miner must stake the number of coins that is not less than the current minimum amount. It should be mentioned that, to our knowledge, the similar idea was first proposed by Qi Zhou [3], however our approach is different.

Instead of sending coins in the coinbase transaction as we initially intended in  'PoW with Stake' [4], hereby is proposed another approach leveraging the "proof of reserve" functionality. The implementation works as follows.

\
Into a `Block` a `Stake` is included, which consists from an `address` and a `reserve proof` of the amount, belonging to this address, not less than the required minimum.

The address that receives coinbase reward must be the same that is used in the `reserve proof`. This is requred in order to prevent using someone else's reserve proofs for mining. To enable validation of this requirement the coinbase transaction's `payment proof` is included into the `Stake`.

\
The `base stake` `S` is minimal, and same stake can be used several times in a row under these conditions:

same `key_image` of the each `ReserveProofEntry` of the block's `stake` can be used in last `N` blocks only `M` times, which is 

`M = A ÷ S`

where `A` is actual stake, and `B` is base (minimal) stake, `N` is `money unlock window`.

Lets give an example, say if current minimum stake is 5 000 KRB and a miner included in a block reserve proof of 10 000 KRB, then he can mine 2 blocks out of 10 last blocks with this reserve proof, then he will have to wait untill someone else mines few more blocks.

\
To prevent the sending coins immediately after they were used in a block in order to generate new `reserve proof` and reuse them, it is required that the outputs in the `reserve proof` can only be from transactions older than `N` blocks.


## Defining minimum stake

Abovementioned `minimum stake` can not be fixed and arbitrary guesstimated, it should be deterministic and dymamically adjusted by the current `supply` and `reward`, as was initially proposed in [4]. The `minimum stake` should be based on economical security of the network and profitability of the mining.

### Security approach

From security standpoint we need to select such stake level, which will ensure that significant percent (at least 25%) of all emitted coins will be constantly engaged in mining and securing the chain. Obtaining large percent of coins in circulation in order to *monopolize mining* will be prohibitive.

Let emitted coins is `E`, then emission-based *stake* `Se` can be determined from total emission divided by estimated number of mined blocks per day `L`, divided by `P`, which is the part of total number of coins in circulation, targeted to be engaged in mining (for 25% of emission `P` = 4):

`Se = E ÷ L ÷ P`".

### Attractiveness approach

Mining with *stake* must be economically profitable. **It is extremely important to select the base stake level that will be attractive to miners.** Too high `minimum stake` will make mining with *stake* unprofitable and unattractive, potentially leading to the spiral of death and blockchain getting stuck. Therefore the `minimum stake` should be defined in such a manner that it will ensure the profitability and attactivenes of mining. We also have to remember that the reward is diminishing over time according to the emission curve, and this has to be taken into account to retain profitability and attractiveness as well.

The *interest rate* `I` of *stake deposit* can be determined as:

`I = R × 100 ÷ Si`,

where `R` is the current reward, `Si` is the required stake, optimal for profitability. Lets call this profitability- or interest-driven stake `Si`. The desired interest rate will allow us to find *stake* which satisfies the requirement of interest rate to be within attractive limits:

`Si = R ÷ I × 100 = R × 100 ÷ I`.

### So what's the minimum stake? 

Finally, we combine the profitability-driven stake `Si` and emission-driven stake `Se` into golden mean and get our `base stake`:

`S = (Si + Se) ÷ 2`

or

`S = (R ÷ I × 100 + E ÷ L ÷ P) ÷ 2`.

The part `I ÷ 100` can be rounded to 666, because the interest rate per day is 0.15 (calculated using emission-based stake and reward):

`S = (R × 666 + E ÷ L ÷ P) ÷ 2`.

This is the base value of the collateral stake.


## Pros and cons

The benefit of this approach is the ability of a so-called 'cold stake', i.e. using the 'reserve proofs', generated on air-gapped, secure 'cold wallet'. 

The downside is the blockchain bloat because of the size of the `reserve proofs` included, which can be significantly large. This can be addressed by imposing a limit of the maximum `reserve proof` size, i.e. the requirement of the wallet outputs optimization in order to consolidate them into a fewer outputs of larger amount(s). When consolidated wallet can generate the `reserve proof` of a size well less than 10 KB, and this is the proposed max limit of a `reserve proof` size.

### What's about 51% attack?

Since a miner can get one block per minimal stake, and profitability approach ensures it is reasonable and not too high, it will hopefully attact more miners, which in turn will lead to a higher hashrate, with which a miner with large stake will have to compete, thus making it harder to conduct the attack. So it will not be enough to have sufficient stake to get minimum number of block in alternative chain (current reoganization depth limit is 10 blocks), but it also will require enough hashrate, which, we hope, will not be a simple to achieve thanks to a so-called BLODHA [5], which we are going to add to POW part. Thit will make it hader to implement pooled mining, hashrate rentals and will add some botnet resistance.


## Reference implementation

C++ implementation of basic structures:

```
struct ReserveProofEntry {
  Crypto::Hash transaction_id;
  uint64_t index_in_transaction;
  Crypto::PublicKey shared_secret;
  Crypto::KeyImage key_image;
  Crypto::Signature shared_secret_sig;
  Crypto::Signature key_image_sig;
};
```
```
struct ReserveProof {
  std::vector<ReserveProofEntry> proofs;
  Crypto::Signature signature;
};
```
```
struct Stake {
  ReserveProof reserve_proof;
  AccountPublicAddress address;
  Crypto::PublicKey tx_proof_rA;
  Crypto::Signature tx_proof_sig;
};
```
```
struct BlockHeader {
  uint8_t majorVersion;
  uint8_t minorVersion;
  uint32_t nonce;
  uint64_t timestamp;
  Crypto::Hash previousBlockHash;
};

struct BlockTemplate : public BlockHeader {
  ParentBlock parentBlock;
  Transaction baseTransaction;
  Stake stake;
  std::vector<Crypto::Hash> transactionHashes;
};
```

The example of the entire block with a stake in JSON format:
```
{
   "major_version":5,
   "miner_tx":{
      "extra":"0119be8e2f2cee3e418d02374dc7ad3fc8c5a601f424f0b49de266ed9521cd7d6c",
      "unlock_time":393,
      "version":1,
      "vin":[
         {
            "type":"ff",
            "value":{
               "height":383
            }
         }
      ],
      "vout":[
         {
            "amount":1,
            "target":{
               "data":{
                  "key":"f3de8efa7f5f156defe25937f5722a4bd3777d4906b18015f8b9b98d5a1d6bc9"
               },
               "type":"02"
            }
         },
         {
            "amount":30,
            "target":{
               "data":{
                  "key":"f22b4f8596262398820aac2d6bcc02018c69f6ab3c23c475b7e7ccf43ee50b7a"
               },
               "type":"02"
            }
         },
         {
            "amount":300,
            "target":{
               "data":{
                  "key":"2befa81be73a978991e960b8fe83de64ae8d7a7f3e95fa367b660c950dcbfb34"
               },
               "type":"02"
            }
         },
         {
            "amount":9000,
            "target":{
               "data":{
                  "key":"73cdc6fc56ac9578209c9525537f6717f1b7dcf0ea9ca62dfe5d766d5b76a80c"
               },
               "type":"02"
            }
         },
         {
            "amount":400000,
            "target":{
               "data":{
                  "key":"30a8f30ae79f0740fcd9a9a7cb077ab443efb25f87b97496705ffea28eb2e37b"
               },
               "type":"02"
            }
         },
         {
            "amount":9000000,
            "target":{
               "data":{
                  "key":"0482eb486c26b1df5cd4fb884fd915503b7957635a2b13e77693cc7ceae6dba9"
               },
               "type":"02"
            }
         },
         {
            "amount":70000000,
            "target":{
               "data":{
                  "key":"ccf9fd414298cd8315e65da4f841124a8478278becd41232d2c4e4d3562dddb5"
               },
               "type":"02"
            }
         },
         {
            "amount":200000000,
            "target":{
               "data":{
                  "key":"7c23b44c04ecfd538b9c8310100c1c9bdfefd239065e59be5d5e5df591c61c7e"
               },
               "type":"02"
            }
         },
         {
            "amount":1000000000,
            "target":{
               "data":{
                  "key":"ffdf1f2604160d77fa548174b69941c7528e2aee9609b608491eb434eb5da0db"
               },
               "type":"02"
            }
         },
         {
            "amount":90000000000,
            "target":{
               "data":{
                  "key":"df3efd4f0deb334af823c16e9e08691e5a051aa754f915ec6e6f2e5cd44114a5"
               },
               "type":"02"
            }
         },
         {
            "amount":8000000000000,
            "target":{
               "data":{
                  "key":"4bd932c5358ebb02430c9df06ef7fd02fe619a5585c3a82e5cb28992f2bea8b0"
               },
               "type":"02"
            }
         },
         {
            "amount":30000000000000,
            "target":{
               "data":{
                  "key":"b9540dca4312000fb81edd1b1276e9c95449b30de56556233fedcf9d1b7e56b5"
               },
               "type":"02"
            }
         }
      ]
   },
   "minor_version":0,
   "nonce":"1cea9f8b",
   "prev_id":"4740e278d03e2ecf2da2b172b857167e157752f0681dbf666f5774f29667077f",
   "stake":{
      "address":{
         "m_spend_public_key":"51fa30b6400097e1396fbac0bddee1a08baa108d4d3084cc9b046744741442b7",
         "m_view_public_key":"2c33cff9395aa946d71fb0d719bbb7aac534a4003582ff7e8a3b0a588c2f3b48"
      },
      "reserve_proof":{
         "proofs":[
            {
               "index_in_transaction":12,
               "key_image":"f60bd2cb3f8e839faad10ab09b76a7cd7dd05ca35b8885259c92fa0eb72134cf",
               "key_image_sig":"b14f2cfaeb13b218905ffb592ca3dbacbeb19d0ddd963267c680c03f2881b9064183a593948e3ceabf95eba4ffd53c5791effa63a88402f6b405717daf7e3a0d",
               "shared_secret":"d283d195960591b66b0a13d798636980c4f15bdd661f009543d9d7ee351fb44b",
               "shared_secret_sig":"f7ce9dffb2cfd381682701e178c90202af0bdf4c158f631413d58e77b4f146043db0d44c40aeccc3ea291afa1f673777c2b06332d868dcb11f1e0b328d39df09",
               "transaction_id":"04e731e344bbc6974cb1b55ad8349d9e3f6cf591b5e0de7601036a946cca0989"
            },
            {
               "index_in_transaction":12,
               "key_image":"ad63de55ef72d878d2dedfa01ef8f3fb1bdec673b778e3516a268aa6c5170c93",
               "key_image_sig":"3e23a377a7a6312968e780ca0e1a7afada7ac26cd024d1fea339735372053b0e789a8ebf295972c93bca0d1a9d52cba904ad78d9140400c3e19acf03e4ed2e0b",
               "shared_secret":"4279afc77930b8a0aa375c44229c10f95809191aa91f12749418b8f24c89eb82",
               "shared_secret_sig":"eb17f2d4964a614bfbe6ab84475400f647f052ed07fdec3bf935459c429c9f047ae09df2e9ce1594d50cdb155eca7ce26d2e5e1761df6461878e1568b9177d0f",
               "transaction_id":"8b393b86d3b06647bdee82eb93cf06ab2831f2f11d14a2bef2f112a852a17edf"
            },
            {
               "index_in_transaction":11,
               "key_image":"a40084402c0c207e7bd94c366e1d67d04c1fd1c3625f71d98755caf62c55e46c",
               "key_image_sig":"8754d3cc92b36de0a5cb7714c138b64c93a96db8891b2bc0e400de7fe222e80d0d9d3592942a638e322735b6a0c62201e225ea977290f9803376e23b9b621205",
               "shared_secret":"cc72664b776902b3584f41854e2032b270f4efd953fa85e48e4629a49e8e2254",
               "shared_secret_sig":"8061f6ffa608f3618c880470e6fc2ba6655daba4636446fe0677eb5b9082bf0e18627247ac18f5a51e1a7d0c6e96f9757e418a86974f6d8bf73ffd8db0b0eb03",
               "transaction_id":"4d95458966cb23a5083246b4b5e2c210f446f94c891c79c03d539fc3d78ef5d9"
            },
            {
               "index_in_transaction":12,
               "key_image":"3368bd5fd35dc55b94368e5130aa6654a387a6af2cffeade108fdc9f2980835b",
               "key_image_sig":"8e959141b19ae4e80dd989523c502127ac5a2275bbe64e44145f5b404d9f5600757627c6b6d931d3e6dc24047ba7e385b1e65156bc6a53b1ef0d3976e238e707",
               "shared_secret":"e248e6e1d4e3a528ffde42bfe051fbf397c26398dc25e31ffce77884170bea14",
               "shared_secret_sig":"b0b5e937e7355e676d48865cfccfbf1a7cc010c88a3489b877144be8aeb1280a602ff204383870f1f88d20c081a1cb69feffe9ad131c4a702e19d92d0523600e",
               "transaction_id":"6af247eb7e0ffcac046f855d69eda03847f614ffa06452256f51e0eb4a567b83"
            },
            {
               "index_in_transaction":13,
               "key_image":"ae62c6e0edebb624a9a85f67cde4069d8d5c9d6bebddad408044d31cee0b1743",
               "key_image_sig":"499327576fdce17923f234a93735c56fd25e2c15844051e8c23f16161576400046b2489e4cf24e23881b63bc19d21d12a09592615afcb30ef0d4800fe291c308",
               "shared_secret":"c18763fa45dcf32cd736e50e4ecad61f9a6d3f41073293fbbf7fde828e2bef6f",
               "shared_secret_sig":"84049476e400c17f5a601b5b770aa5d019497b0a9c403892c99871c50828d60cb452513b4985198ce65a9297c125366a13eddaf2779d549def2ff160fd202800",
               "transaction_id":"9bba92513b19f4fdcfedec2d73871884f220fb6c4e99649d265587aeecb55da2"
            },
            {
               "index_in_transaction":12,
               "key_image":"0f83e676bae3dcdba930c4f9f6b530ca9fb9c18650155dd337246bf4060e7470",
               "key_image_sig":"c0e57b6ad84b8d9573a4aaac82a3eedf1f3268c511f8a769be8686c5c1829702f10da287fc15661e7096c1717c1d5f69989a084b6da0faea37ccf60464027505",
               "shared_secret":"98633675a2c7195f8e024edcca823656e1183d34ed7c1c4f28ecdaf3aa9c3ee5",
               "shared_secret_sig":"f123e69f9289fdaa35c993fb7b988c59c1b78503e859fa16e9bd164892da02094f0d5c82b27ab81b98c8ca2879e61f6e4aad4e1e644324912b2724b39a375004",
               "transaction_id":"e05be9c9faf37c9619b0419883e127c5f8ed52c058ebd2c7a1fd0eaaa3aba2cb"
            },
            {
               "index_in_transaction":11,
               "key_image":"c0f56e3cd4c6709f9a7194a043e070bde0d030af1935a2a9c1d50372aa48b9e6",
               "key_image_sig":"9438d0e39f78b01a20429c7d73bccc4b5516ca7a694cdff6507a2b068cb13d0c7612327662b454bbf2d0302ae5976ce2fd6ed382f87fc2506af1ad837bc4aa0c",
               "shared_secret":"a0bc1d3b784b73c697cdd2253e33646afa3a3d022dc22c2c765a4bb91fb359e8",
               "shared_secret_sig":"0ea8a6a4994060698f79d95d1b32a0a0dfa2326bd4149046dc6587eca71aa700a6fa4e57298b9794afab98c8f6bc816a9342bd92fe4bd91dfa0c6f980d684205",
               "transaction_id":"e672dc348f420d1efd9472dc14bde4c25827415f091a40e2f97c0af09d4ee8e9"
            },
            {
               "index_in_transaction":12,
               "key_image":"2bdf97a626ffb489094881335d73c4c9556bb92e7fd3eab25bb2946e9baf9031",
               "key_image_sig":"91c39df0d17e010ad0ef39c020b518864037eda4d84fd7c2c9afcece1a97e9043154273e1de4bd602c8b8ff4f2577e710238ff06d0b4f1748b6a9ca3381ba309",
               "shared_secret":"ab1b3f050025cb7526a7016cdc95b45585786d4f7f59f6f6c627e6a808f16136",
               "shared_secret_sig":"bebc605c193f6b7bd618f280c404b4f12ac61dd1e479bb4b399e1fd0ea67220657d5f9e0655f5389a897ffd47bc8871cbe6f3c5eec8da321df55f83b3884eb00",
               "transaction_id":"20822bcf43e84370a1acb537dee97fc4b6171687c43d533925e33e866f656fac"
            },
            {
               "index_in_transaction":13,
               "key_image":"9b351c9ee8d7af3d26cb8c23accbb0d8a3f8d7ac19d96974ca73c5cebeb24af3",
               "key_image_sig":"c2122a40e33fc0db24ba21b8298dbc92f6cdc43955112d9c0a27131d0b709900427e386b41705d6dc79f1b93d74d898db8cf90a319f7380a3b5a569d5c738100",
               "shared_secret":"06cd8b6d06ad83db9cb92b0be801363af3ac795818995a70d3993a983417590d",
               "shared_secret_sig":"9dfc8b5d322cb647046c696a6070ec6623712eb141df61b998a8959be0fa8a0d326e93dcbce1824f52c11f97f194ce1d2115057068305193e841ae8dc7fc140e",
               "transaction_id":"d4dce27aa1a0fdb3f53380210c730825c3651b0e2ab5b5d3c39eb064f4779451"
            },
            {
               "index_in_transaction":13,
               "key_image":"c326177683cffb3ec86f0e412ef0996f49a595368a17d3c6a3f28e30ab2a8352",
               "key_image_sig":"9b0254b4f37beada4dbf6ec770637720d65a0108ece792b2a992073047aaa803e0cf490cc54de59f7bace767800e583d9c5b20388b17aa1e9af543d54dcda60a",
               "shared_secret":"6d5fcb92e110b2a61f02bdd80615b4cd3a156f40e64b5da3d946bc50c144b858",
               "shared_secret_sig":"5580a6db6b2ba4fa348c3693cbfd74d88ab20cb060eebf706fc731bad2a7e403a61a67319fe4cf273e5ecb707818268207a5757e6f41abaffee79159ea2d7d02",
               "transaction_id":"ce8fc7485a962dac22953b33cce553708a4981ed5c377a29811375352fba37cd"
            },
            {
               "index_in_transaction":12,
               "key_image":"3fe32306f3341b8cf8c461fe02af980ef2c1d51a7fab8e6077c85828128956e2",
               "key_image_sig":"9187ff01e643e53774ea03a0c84a1b1710293bcf9331d8cc6e4e093c1a465c0f7f0da8aabcfebb52fee0d46434c7e5f1f0feb7054f48bb5359b06d18f3507008",
               "shared_secret":"ec7a840664dc703de478f3f2ce8bd8f9cac2bd8eedbc90ba144306ef88ded656",
               "shared_secret_sig":"35583362557258b99afa6aca5aedaae33dc92359610e080b86099d7314ff2703406c33c80c5d240ca31e5cd0f65ed5aefd763cce7615eae1c2cc5aa58e94dd03",
               "transaction_id":"23a8527c2e553b5e891b6e498ae0a7b1ebffdbc805c5fd1c5eb345f65f54f155"
            },
            {
               "index_in_transaction":12,
               "key_image":"f0753ece4ca37c75ba8effc389192f53c353f7c21a5551f071ad015f7ed54569",
               "key_image_sig":"33407960725fbc94e9b2a66c55cf13ca995901d6d2ed9075fb0a0dcc83eb64085319aedc66312296df298731378a2051b32698e5ac37462d065d746443230c0c",
               "shared_secret":"59ab046695c736928f1d720605a3c40dbbddb39a12c31c5bdeacb152a1a22591",
               "shared_secret_sig":"d32a2e13eb76b3b4b05952823c50866d926b7115eafb5559fec0628b7de09b0d16b2548117c828c51e4c1e9e3e580e56a64a5254b3cf91cff091ef0ba986280f",
               "transaction_id":"e6c8df02023b615da3bae2d3686396d7a2daed0fff481fdfb67921ed16cb57ef"
            },
            {
               "index_in_transaction":13,
               "key_image":"da5a77945dc24d4900e394ec9ac95c54fe6bed9041dbeec5fea10c4501b25ef3",
               "key_image_sig":"4c94827af34ed1f8424c804050e907f21b5c032331fa3b83b51ea1a83567490ba3e6d2a9e2c97720de211241d9b62813daf6e8c606200d44cf7dc9c4ff821e03",
               "shared_secret":"ba6514e4151b1cf15b57963afb15f0a83c0489c494400ec154529c8504210086",
               "shared_secret_sig":"b465b6a88584faf4e071a3c5b3aac8918ecafb301c5279a26b16d9d242f6f10933be86f889fd3f2d5feb5935d3703711141a9fd3a24818b022d19dcc84cbfe0c",
               "transaction_id":"806260882504a221a37c240aeb31aa506a907cba857972a6a5eea8e6c670ee69"
            },
            {
               "index_in_transaction":11,
               "key_image":"defd97185c3c7cf00f073dc0490841efbe12a43af5d4d2c91073e13b7c5971a5",
               "key_image_sig":"06428deba8c8adb2eacb00de5224366d35a1f38243b23b06652be9500ca0810dae2262ae55c1afe5c4ee6c2f26297e34f5c8adb87f7f575735ca36f067bafb0c",
               "shared_secret":"3cca051a619ddb36dbdba7222076d6e36972396f61fae0485f71e26b21b2f29c",
               "shared_secret_sig":"67cf65d98f2dfb9bb612fd6fefe15cee90101d4747006de9dae9d6d803ecae02958bd7a76f3ddceeb40a6fbf06ef72e0c1cdbc2f912c83121b091d9d90a1a30c",
               "transaction_id":"b0bf36f963dfb0c9f93de4bf9c54af989184065bec4030a541e99a4bd87f8b0e"
            },
            {
               "index_in_transaction":12,
               "key_image":"21d5ead54c6fa514ba69e3670faada2f901393214df106235b42de8e9606e88a",
               "key_image_sig":"e73e8b0ad4cc1807f61a667ac3a9bd51c714fb56dd4cec140f49c1d2b69b7905989d5b10814f19d1c85e050557410b77745da604ea105ab8f8eb047692b9ab0d",
               "shared_secret":"0069a9389bb31dfa9b287c7b89ade4f665da5b0fb3898a94698a15c92efbf391",
               "shared_secret_sig":"3bd6a524935f7a1c0f123ef8147a253e5cdd8b486bbcf19adba602051a807709823d1e47a781ad9ebb4a94404fadc6925066ca3772a4dfda837eec75e0479c0a",
               "transaction_id":"89b9d1e385af06ed7c8bef22f665d8926ccab7b470bfd66b986a76f297a37d44"
            },
            {
               "index_in_transaction":10,
               "key_image":"45a631731d19891681aab81cade248dc9f56ab6c8039ab605ff6528a11bf3c01",
               "key_image_sig":"d108a3b11c26585bfe69029d5ffe7f20b6f47bddd9bde5536f7502802cec5f0dc17b08817320882725ba46ccf5ce4ecd43c8a6fe809d5b3fbca400d11423fb0b",
               "shared_secret":"e1a8510bde595264acfb80c00f48487944e197d4cd8e539d03325c4ce241f20b",
               "shared_secret_sig":"c18f263c26e2291e24cb96ef739ca626701ebb96876589d54c4fe679493cc304811ab4c11c00bb2e5939bdb511a656ec1b1a0dc3fe607eb5d54362e7b889420a",
               "transaction_id":"312f83313cbcc2321a0fdf65a50d2861c40670402dc14c058a1ae1436a3b16b0"
            },
            {
               "index_in_transaction":12,
               "key_image":"421048572b0f53fe15b3909bdfed4fd8d3da5d3d35dfa691d47ca8b9d5c35e7c",
               "key_image_sig":"e93b02dc8878b34da4ed4a0ca1146df993815971c94a0aa35374d907c7405b05132f4072c356a14f4b5b697e64611f4835a6071dccf1df505c72f4d00b1c6f09",
               "shared_secret":"3f9c00633301a17e4799649bcc03f008a88c5dc1ae4e9c6538c6e9fa94659fbf",
               "shared_secret_sig":"31e40c291f365f268a38115f98e1a90aacf4dff996a7fabd8dd606057a1412009890145b45148106ac0f4f1e40370f78cca344e78335d0f56692a19597c4a70c",
               "transaction_id":"8c48135d3fb0fcc933b5d0f2543bd2aaed8dddd9d72b917553450d55451cbf71"
            },
            {
               "index_in_transaction":12,
               "key_image":"aea7273d478db5c493a8ea167085ac4be00098e3fb64cd315a891950d6e05905",
               "key_image_sig":"0fcccaf8e9dc68ba0355b63d2bfad991d2a296918b45c38458ce156e89c8cf0426fa8738beaae338ae7eeb332b7d46a20c4c5475cee02fab77fd7921158b7201",
               "shared_secret":"bf534c43999c0e17d33b37124b9957be31389dbbd1a7d84170fcee43e094215d",
               "shared_secret_sig":"35d015e87bb73304a0fda2e9027844c09b4f3f109c052c365641f2eab444070c8ddd7dd5bb508afa32ce0df35f251fb4abfe5ead6ee3431ada480d94f4903c09",
               "transaction_id":"208bdc996de7459c0cd4728a8ebd50f13a5e5d4778bc3261ca4fc784dc37759e"
            },
            {
               "index_in_transaction":12,
               "key_image":"1ba066c3a857b1e0f44a5a012c1bc6ee8dd33d79f00363fb9b0f49664dfba9a5",
               "key_image_sig":"913bf464f1ce90725ddd4778505ee416bc6e924177833733c64d4b080cc6a2062cb3888930d4d843ab0284e4fbf60d1586108f81eb6107dde2763773346f4907",
               "shared_secret":"cc9871c91a24bd71e1df5e4cd9d330f31d49686972159dfd890c4ee113492b2f",
               "shared_secret_sig":"83ec9cac50b1525cd0d8c3b209962844d2c21eb66636630894739e8bc65c2900b9c063d83bb3161f7bc57b8675ae775929b3c615d79814b728bd8e275a550706",
               "transaction_id":"20ec002dff3cda42231c03c1a6b3a15304175993261191bc8016cf3284ca4846"
            },
            {
               "index_in_transaction":12,
               "key_image":"d3499dba65bb5f3345143e19c399c4c3902a0fe0aaef2d358b0f5096e25dc348",
               "key_image_sig":"4191c15eb23e525e81f8f7fc4b6a52657cc6402497ffe0326258a6e04a5ab103bff5177b77d120aa9c1acb73c106441549adef5fe057afc5875208485662110a",
               "shared_secret":"7742dd31ab77425f5d0d968fa94ab6451db45018c6452d49d2fc79a4e14e8675",
               "shared_secret_sig":"5ad354421437626f79dd0798fb10c1b55a6ab31e935b5b45dea765a258d6bc08926e769d82395fcd4e1eeb21918592f79a8afa5b875469eb4a6482c59944db0d",
               "transaction_id":"2193d2100a963f8a400f4011396b27aed20a974ee9a5580fcc0355249581c722"
            },
            {
               "index_in_transaction":13,
               "key_image":"a5d9205fedd0d15fea7c50393f6a9b5d1c1b379f279e4552e1574922b227a016",
               "key_image_sig":"fec2669951325bbcef0a6e18e8c72d57426fbaada8442632f692507df323c709804c920f58f172674085945919c78896c1277a6733c737ec8d89490052552805",
               "shared_secret":"910e483ab298f2ec8e127c3043c627bbf7f3b26ea4e266ae2b8461e95c68ca7c",
               "shared_secret_sig":"1c23f878728adb283b5ec48d9afa82839169f2993ed524ad036c52f639e4570bea849a52145caaa0b8146c83bdf2b833b094370807231392ad2cd6c532377707",
               "transaction_id":"0572810d3e4d15f25489a7e99bc1f49e1e30bf91d67f6a3440f48c90df850e08"
            },
            {
               "index_in_transaction":13,
               "key_image":"c6fb1b97258368c91bf214ef439423f90cd71504e0adcb595af9a900fa6f1291",
               "key_image_sig":"40e8b40e48b62924f646c2a6479aceeca4a81d5665d5c0dc27a6b06aa417170c9c94bbac0fb12b0561e74e4ae1bc1a7861b77aaf30bfa81c2bc643a14fbe8f07",
               "shared_secret":"fbe0120e198c20167fb0aba92ed2f0e66ba1b5f5d5075b190e039d26708f94d1",
               "shared_secret_sig":"e90effd10cbd4b73dfddfa1fe25ce0acdbcddc174cbda3efd5c7310434cefb01da5dde32049d6cfda344d178ca89d2c16ab65645f4ced5f4541f312f5e16f405",
               "transaction_id":"875804de1b27fc36b7564f101cd93b6a2873054b8c2ced00221f0742a2f349f9"
            },
            {
               "index_in_transaction":13,
               "key_image":"363fab78550682c77306a287eaf1b85677ba6e7a0c228e3b5acf4e5bb0227eec",
               "key_image_sig":"3c0703356c5410b5ccf4a9821e06937026a5e51bb7777a6ab6f4e6ca13a0c80fc9d24fe35ba09356dd7ee1df93888ed0566bfb9f714fbe46936662f63f13e40c",
               "shared_secret":"f40109b66ecfd406375579d1b67e6bab53e4eedd75da89199e9b1be431f11746",
               "shared_secret_sig":"b13c509b772086f78d51a69c66af83ad47e38db3aa686bb7e528aea1e9d2df0046bbc96cada93f01d299e26df6f97b9804d6395a3fa13f0c8a4d633f5471de0b",
               "transaction_id":"b8dc0a2193a5ae26371581a9c81bbd4ef027e2633c9da0dc153e2c6d9209dda9"
            },
            {
               "index_in_transaction":12,
               "key_image":"900c229befae325d6fc4a1320cebd5588ca36ea9cce8fac1c80ebf8c68f64b7b",
               "key_image_sig":"df259545fd35c8b84f6612ea2805e8994a5f0700a9b3995f8505aa409fe23f0e0d09c1f916fdb2674ae330a699155b052082e7f270696f7c041d99b350647a03",
               "shared_secret":"7ea35381f3ec335165ea3ea11420171f9ee2d18ec096efcafb79c9e3638511e0",
               "shared_secret_sig":"c90a4cd5250d4e4015eaa6c889361efec75c9d340d862996ad1d08e25f2951097ac7b17415a63bd9d9c85548033117d11968c02a1245ef6f090d563587d6f609",
               "transaction_id":"eeeb54d974aced75d1883ed3d63db1b6be05934c0767543bd5dc43a323cc92ab"
            },
            {
               "index_in_transaction":12,
               "key_image":"a1e54e6a79a7d1d6b0e417d9a9f8391968e2e9eae0c2057bf45ef5af6d1845d3",
               "key_image_sig":"a30fec7c6871ebc691a16502b84bf0a1b6663d86cb7d8cb4a5f471bae416f10566f6c1970ff9fc2535dd76ad638487f492e076ed472979435bec491c237ef904",
               "shared_secret":"e42f12529c25483d058c5a7d7417a27388719a63144b5e0793d4617b51d427b0",
               "shared_secret_sig":"c9def723da00eb3cce423b5ecbc288250bedd176bd54befbf865c02999688d050cdd4ff60043cefe87465fe6f951dba74600339a28798f559061cfb561ece60e",
               "transaction_id":"ca5f4d8923b917ea796bfd2889fdb710bd94266188c703e1545f7656cd0bc670"
            },
            {
               "index_in_transaction":10,
               "key_image":"c36117346b447805debd5cfa67e060f46d82bff5682c85d9753ac2cac4271af6",
               "key_image_sig":"e69f5d97d6692fab78b8375a2d767672b80a700fd3101d74060f5e29cb30a40c666d9f0763965a816280258a476d8d54ac835ebd62dfd23cae4d81c93ea29f01",
               "shared_secret":"d84bb086aa4b7e8e70b487c653cb315f7f406234d8f53d2017e787b265f9abc5",
               "shared_secret_sig":"95a1e081414f30d2b8625db34cadd5b78308b48cfeb52cec252d7335613904075dac1fd9eff225728fd542145c4871689567e4ed7566d3b009b20658c66e390c",
               "transaction_id":"e4f3c614d41ffbcdf1cb92913ccf0c0c38d1213acd075f8489276cf28c148071"
            },
            {
               "index_in_transaction":12,
               "key_image":"5e73120ecdcb5c26c98df071d8265b07e8c0ea97b96be6ed0d42a4d28522821a",
               "key_image_sig":"bd72796ad40b58de1ebaf2b89c220870b61b4b6824c40bf83ce13130bab7ce06d18bf9a14c08196b00d4898fda7a85fc2fb89ea238c08afd3b9c230e1b3b0a0a",
               "shared_secret":"73e3d390088fbbf9ebd5a4e88857fddf3931f4ada0e3ef5ee4e5547e1591c828",
               "shared_secret_sig":"f0766fee9a5968ed175ab7c43c4d4a1a45e6de3b1e709087fa768dc5000e51044ff4408555efb2a615180afc95a8b40cb055a350e90939493021910418642d0f",
               "transaction_id":"50fd75cd6d961e0f0b8b6a59877c65a9017f4df643859c49ccf2752b357ea082"
            },
            {
               "index_in_transaction":13,
               "key_image":"29e8dfb40749f68b8db16e43ada69c0d358da08255f03147d133e2fa41b3755c",
               "key_image_sig":"b1fde559f1b96629adfaf460427ba2cdbb26494da23e786f70734e5b58ae3c043642910197b292790bdec2713ee4166cdfbd2ed76b665ebd34ecf3580718ad0c",
               "shared_secret":"509b9b2b7f11c8417b04a2046a13e4a52103a91b354af7f631ed5f398da83e03",
               "shared_secret_sig":"ccc4ad8b833040bbf8f1002cf487fd4c1cc10da74ea9ed3cffe8c01e556019042de6be1d7ad5a6d6888c6c0619c5a4080b5cb9bd0b5665a80458b1d356b1dc00",
               "transaction_id":"0444d793863f4217aef577fb8d457a5bae7264fe05f4220020f13880dc248ecc"
            },
            {
               "index_in_transaction":12,
               "key_image":"0e65cd8129ec69f09ddb029f911aa35e963154c86a2edf444c21b827beee649e",
               "key_image_sig":"e2d3c47e279829a94dd3387e4c35a4e707ccb72ecc7e18f05a5a420abc61800b245a664ceb4f2a0f6910d3c3c9e25ea4378722e4d8ec399bc2d39296bbde500a",
               "shared_secret":"d7057aa0a766817463a6b8973da13cad30cac2af76020f7f45d6982aa0d73487",
               "shared_secret_sig":"536044f31b6836bdf039643ac57eb401557cd91c59255e04de9827fd972caf0990ddeeb6b90adaf2d56f10ee57956646c1483d80c61c72733f6ce4ccd622e909",
               "transaction_id":"8e7d47779a60f78cb3a0cb2b32c1137062276a40a7af689df648b23d87a99b01"
            },
            {
               "index_in_transaction":13,
               "key_image":"ae1e44001041aafa1720912ed1ab5adef3507401101fb095b65242096025b5d2",
               "key_image_sig":"97c23bade731e76a3a7bc22860cd430d03529cbf0501068e80715874424d6a09568644ac7460a1c1d35010e9a9fc202aa86153033d2ea38cf6631d6ea11e2f06",
               "shared_secret":"0b986575733f1050e0a5c06c6e0c5e9bf2ff8fda96b16a013362e9c877342f53",
               "shared_secret_sig":"45f67a59b9d2c54d089c60ff3045ef13afb5a6b9132e76ba52aac4a951c9ee089b114bcad5c8930247b775eeed28b5400634d4830981f949388ebf1b39aed10a",
               "transaction_id":"2341c97421dbdeab27b6f510c99f5a74826dd72ddab87cb5cbc0e094a3694c77"
            },
            {
               "index_in_transaction":9,
               "key_image":"1aeb69081a10cb65d8c9fbcffb9038f0a190d2545f249c9962afa4c7794b133f",
               "key_image_sig":"969506a291ddc865c2762a29189870587a8e2f7e4d6536c4a56a358b04c3320144e9b3b428ecea8de766d6e7d8022f850e856c1a5026419ae84e9d2b885c4a09",
               "shared_secret":"8b82f88fcfaf9ae95c5a5428a3bab5c1919a79d9c3c2ac76556fc67c05d94668",
               "shared_secret_sig":"69294e225f7032062a1f6177167d07d70b53a05fd93ba71909c2a787fdb04a09254f20c603041ce37ab1eae973f05d85baee4e1c23b1360c9112e3c9de573605",
               "transaction_id":"03748ef72badef6bf1fdebe90c31c491ef14e67ed1f21570a40e50c794cb22a2"
            },
            {
               "index_in_transaction":13,
               "key_image":"0dc20c60385023c44cd94413713987c966ab811584cbbced9993a7d18ee1a5dd",
               "key_image_sig":"192b423213cacac71e4a577e1fec3e11b2ad0faf70a445347c5ab319dcf548037af63be08ef064cdfe5d56b68a94b3b6bccc290c8118a11dcd3556b54b480d08",
               "shared_secret":"f0f55a8c845a01a61c2232f12d8962b841a0e06e1f6973102ba67a951b1035eb",
               "shared_secret_sig":"bee6c07d40218d27fcb99a791a6fa3b710c5201c5619487517bb94d7973e3b0050a37344f3a15c667e1d3e3d0134e2ac8936fdf4bfcd874c80e76ca344aebe02",
               "transaction_id":"8fe09c66d35aea191be0c0910b2f7c2188bdbf16411f4e6737b473edd532428c"
            },
            {
               "index_in_transaction":13,
               "key_image":"2a70dc3e10e7a417ac45f1b5f10a24bb2ed4ddd4757c124e969ac2f93dfcde2a",
               "key_image_sig":"561191b6dd53ae168d7a4417f9bc4a1175e09d85769f6a2b3b29197fcc6bb20e71d9fc3c34c90ec174864cbc9197b5e5e3f8bf0400c44a844ed0c397c7e80109",
               "shared_secret":"6b70876b0321f2d2ebdadbd30a87552ec47f9278400d6d06a4b2f8c6442e92bf",
               "shared_secret_sig":"a98bf9dfbf9a5c2829d1989628636a2df85ecebe2a7c97149dadee80a549cd0c75bb77878efc76c0cbd54f0fc2f0318af1b3aa7fece46dd0991d0d46d7ec540b",
               "transaction_id":"4f36c6ac7d3afcbefe59605dafa8e18bea5e02fbf5559b44282562ca0a654c4f"
            },
            {
               "index_in_transaction":12,
               "key_image":"0c5f47809785bdc595a2f2d707a71a4cbeb0e1467cd4f5c2bebef58377628703",
               "key_image_sig":"85de5ce608601d97e05314cdef4abdef0e950a22ee8e7427d49fefd05e18600e8cd4ff504fd92dd2dc8298a32e55dc1515eb32ee710510e758702b709f69e401",
               "shared_secret":"43f8372b98130980844adb9c4d44187a531fafcfeaf6ee2db74afcf8d7034e7e",
               "shared_secret_sig":"448db20a19f6589d68a2e0b0a453e2210a9a6de0d055a78d14e086447d7da2087acacb5362d259e13972937c2c66641ccdc679f954f4789501d30ec8d44a2b03",
               "transaction_id":"8c03fc8ceb503805a9054edb6b2f29cbb0507cb5990ed1d483e679e4a170330d"
            }
         ],
         "signature":"9aa3a6217de1a7137a8532b0c2f45cc2fe8587cbb124d37e79fd3a90e31f550925948588d87f3331a19e4c13e335cabf5645a520f2b05cd5120878306e29d006"
      },
      "tx_proof_rA":"9da32079e2151d4ac820c7bf634919800a20ade81c7088f532fb300f9af3e957",
      "tx_proof_sig":"8ebad790c19beb7b0e587c9374a8633ac77c392642a4c6bfe2d27f085109a40c010366ca80183393ffd4f14145869b846e62046256bfdfd50fef2210dd53b703"
   },
   "timestamp":1608541671,
   "tx_hashes":[
      
   ]
}
```


## Notes

A variant with pair of mining and reserve addresses, where mining address must be in the `message` field of the `reserve proof` so the `reserve proof` is valid only with this address), is discarded due to various risks of abuse.


## References

[1] https://twitter.com/zawy3/status/1082199522812612608

[2] https://blog.coinbase.com/how-coinbase-views-proof-of-work-security-f4ba1a139da0

[3] https://medium.com/quarkchain-official/proof-of-staked-work-ef36f9499279

[4] https://github.com/Karbovanets/papers/blob/master/POWS4.md

[5] https://github.com/Karbovanets/papers/blob/master/KIP-001.md