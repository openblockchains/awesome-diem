
@protolambda <https://twitter.com/protolambda/status/1141010805716082688>

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

