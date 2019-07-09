
## @protolambda 

<https://twitter.com/protolambda/status/1141010805716082688>

A first technical impression of Libra, aka "facebook coin", compared against Eth 1 and Eth 2.,
18. Juni 2019  


0. data keying:
   - 32 byte keys seem to be the future. Not only Ethereum  2 is using them in SSZ, Libra will use them for account keys
   - static generalized indices, can't find much for "light" clients, you're either a powerful validator, or a user (to be expected)

1. Hashing/merkleization:
   - SHA3-256. Not like eth1 (older SHA3), or ETH2 (SHA2-256, like bitcoin)
   - No derived merkle structures ("hash tree root" in Eth 2) just bare binary trees.
   - Sparse binary trees, with leafs moved upwards. Efficient, but not static enough (important too)

2. Signatures
   - No BLS (like Eth 2), nor ECDSA (like Eth 1). EdDSA, edwards 25519 instead. Seems good for small values (roots), but like possibilities with BLS better.
   - Signing transactions seems to be very similar to Eth 1. More on that ⬇️

3. Transactions
   - Transactions are pretty much the same as in Eth 1.
     - "Sender address, pubkey": tx.from
     - "Program": tx .to ++ .data
     - "Gas price": tx.gasPrice, just in Libra coins.
     - "Max gas": tx.gasLimit
     - "seq nr": tx.nonce, incr. account value

4. Contracts/VM
   - "Modules": Like a smart-contract in Eth 1. Published through tx .data. But stays attached to sender account.
   - Their "MOVE" VM is interesting, has strong "asset" support. Really just a take on var-ownership/capabilities/mutability. Can be ported to an Eth2 EE.

5. Logs
   - "Events". Not readable from contracts, cheap, used to read changes live or back in history. Same as Eth 1.
   - Txs have an "event tree", instead of blocks. More ⬇️

6. Blocks
   - No blocks, but batches. Everything is sequential, but abstracted away from blocks. No POW anyway. And event roots etc. are with each transaction in "Transaction-Info" instead.

7.1 Serialization
    - They may have looked at our Simple-Serialize. But older version, without offsets for lookup efficiency (SOS style) or per-list max-sizes for static generalized indices. They do cover sparse lists as a simple TreeMap, encode key, encode value, repeat.
    - more ⬇️
    - See https://github.com/libra/libra/blob/7c5de4d161e096648dba8ac54328d1568ae2d2e3/common/canonical_serialization/src/lib.rs#L123 ...
      "SimpleSerializer" vs. old SSZ: https://github.com/ethereum/eth2.0-specs/blob/v0.5.0/specs/simple-serialize.md … More ⬇️

7.2 Sparse serialization
    - In Eth 2 we have offsets, so you don't have to deserialize a full MB (or more!) when only reading a small value/part.
    - In addition, Eth 2 is looking to do "sparse lists"; tree-maps by fixed-length index, with index lookup in header, similar to offsets.


8.1 Leader election.
    - In Eth1 we have miners doing POW
    - In Eth2 we have randao temporarily, and are going for VDF based shuffled committees, doing POS.
    - In Libra..., we elect leaders, and "validators" send blocks to the leader. More ⬇️

8.2 "the leader of a round is determined by the proposer of the latest committed block using a verifiable random function". Not a VDF, or randao, just a func with the previous block as input. I hope it's not just a hash. Imagine paying 10M for a spot and others manipulate that...


9. All in all... ok ish, and I can see Move growing, getting dev tooling / support. But if so, we'll just port it over to an Eth 2 execution environment, and let the market play with it.



<https://twitter.com/protolambda/status/1141435774052818946>


Libra TAKE-2. Whitepaper is somewhat confusing, source code is where it's at to get the details. 
Dug through it for you. Not an audit, just fun. Comparing with Eth1/Eth2 where relevant.
Getting in merkleization/hashing/crypto/misc, but not consensus.


0. First of all, source code is located here: https://github.com/libra/libra 
Apache 2.0 license. 👏


1 They actually have a "legacy" and "next-gen" crypto package. 
Guess what's in the next gen? An unused BLS12-381 implementation. 
What we use in Eth2. Work in progress, but everyone is welcome to join standardization process. 
And experiments like this help the whole space 👏


1.1 Libra next-gen crypto collection:

2 Merkleization turned out to be two-fold. The whitepaper is similar to Eth2: binary merkle trees. 
But not derived from an arbitrary structure, like Eth2 SSZ hash-tree-root. 
Reality is: they use multiple merkle approaches. Binary and Patricia trees.


2.1 Tests of their binary merkle-trees: https://github.com/libra/libra/blob/master/storage/accumulator/src/tests/mod.rs ...
Interesting difference with Eth2: we're looking to do sparse right in Eth2. 
Generalize merkleization, identify leaf nodes in arbitrary structures by a "generalized index", 
and enable protocol improvements.


2.2 Libra on the other side, seems to be using "placeholders" (instead of zerohashes[depth]), 
effectively shortening the hashing path up to the root, and is doing indexing differently: 
just an accumulator index, instead of a general index for a nested one like Eth 2.

2.3 The binary accumulator serves a purpose of being updated continuously however, 
not like the binary tree-hashing in eth2, which serves tracking overrides of nodes. 
So we shouldn't compare them harshly, different purpose -> different approach.


2.4 It looks like they get around the need for zerohash[i] in the placeholder spot 
by adding the "defaults_bitfield" to their proof data, still investigating...
https://github.com/libra/libra/blob/004d472284ce7d3a0ce2f577ecd05de56026dbd4/types/src/proof/definition.rs#L379 ...

2.5 Although the accumulator proofs don't directly work as plain merkle-branch proofs, 
the use of the bitfield may actually compress things well, make verification cheaper, 
and enable to proof multiple sub-branches at the same time (with a few modifications)


2.6 So many binary tree-orders, but no generalized indices :(. 
Their code may actually be less complex when you can just mask a generalized index big-int 🙈
https://github.com/libra/libra/blob/master/types/src/proof/treebits.rs …

2.7 Libra-style merkle patricia trees: https://github.com/libra/libra/blob/master/storage/sparse_merkle/src/node_type/mod.rs ...,  
a modified version of Eth-1 style Merkle Patricia trees, also used for acount storage.
Was hoping for optimized sparse binary trees, but well, 
this is proven to work ok-ish with Eth1 at least.


3 Their internal block-tree is super basic: HashMap<Hash, Block>. But also super minimal. 
Centralized consensus = less forks, easy finality. 
Just collect 'em, execute 'em, and when committed: put 'em in storage, 
and completely get rid of conflicting blocks.


3.1 If only real blockchain management, re-orgs and forking were that easy... 
Not blaming them though, it fits their use. Source here:


4. Addresses
32 bytes, with Bech32 + network ID support.


4.1 On their hash-function used in Move and address sytem: 
"TODO: keccak is just a placeholder, make a principled choose for the hash function" 
(typo PR anyone? You can be a Libra Contributor™ 💯)
https://github.com/libra/libra/blob/004d472284ce7d3a0ce2f577ecd05de56026dbd4/types/src/account_address.rs#L174 ...


5. "CanonicalSerialize"
"One design goal of this serialization format is to optimize for simplicity"
https://github.com/libra/libra/blob/master/common/canonical_serialization/src/lib.rs ...
Looks a bit like Eth 2 simple-serialize. But even more simple, no offsets/optimization 
or hash-tree-root.


5.1 They implemented a simple way to encode sparse (or keyed) data, alike to the structure of a TreeMap,
but no index/offsets. Also planned for eth2, but with offsets, and non-arbitrary keys 
(we just need indices), for better read times when only interested in part of the data.



6 The libra VM validator looks far from finished.


7 Node is pretty minimal; just a gRPC server with a few consensus + tx pool methods. 
I see "trusted" and "seed" peers. Everything handled by admission control, 
which 1) handles TXs submits, 2) handles storage queries
https://github.com/libra/libra/blob/master/libra_node/src/main_node.rs
https://github.com/libra/libra/tree/7c5de4d161e096648dba8ac54328d1568ae2d2e3/admission_control


7.1 I can see admission control getting a black-list functionality to censor things. 
But let's don't make the same mistake as EOS (blacklist update fail), 
or bitcoin (avoided double spends / inflation). It's not the mempool that needs to be protected, 
just fail the TX properly


7.2 background:
Bitcoin history: https://hackernoon.com/bitcoin-core-bug-cve-2018-17144-an-analysis-f80d9d373362 
EOS history (recent ish):


8. That's it for now. May take a look at their consensus at some point. 
They use a VRF (readers; VDF is different), and some modified "HotStuff" consensus. 
But it's still a centralized system. Wonder when they get a public audit.
https://github.com/libra/libra/blob/7c5de4d161e096648dba8ac54328d1568ae2d2e3/crypto/nextgen_crypto/src/vrf/ecvrf.rs

