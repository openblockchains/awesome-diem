A collection about all things Libra, Move & ZuckerBucks - Let's reinvent money with fast and cheap world-wide transfers; let's bank the 1.7 billion unbanked, ...

# Awesome Libra (and Move)

_Moving money around the world should be as easy and cheap as sending a text message (or a photo)._


## Libra

web: [`libra.org`](https://libra.org)

- [The Official Libra White Paper - Web Version](https://libra.org/en-US/white-paper), [PDF Download (~600k, 12 Pages)](libra-whitepaper.pdf) 

> Libra's mission is to enable a simple global currency and financial infrastructure that empowers billions of people.
>
> This document outlines our plans for a new decentralized blockchain, a low-volatility cryptocurrency, 
> and a smart contract platform that together aim to create a new opportunity for responsible financial services innovation.

## Currency / Money

Libra is a stablecoin backed by a basket of currencies, and US Treasury securities in an attempt to avoid volatility (and speculation). 
Facebook has announced that each of the (100?) partners will stake an initial US$10 million, so Libra is backed by US$1 billion of solid currency, on the day it opens.

(Source: [Libra (cryptocurrency) @ Wikipedia](https://en.wikipedia.org/wiki/Libra_(cryptocurrency)))


## Org

Libra Association, Switzerland, Quai de l'Ile 13, Gèneve 1204.

> The Libra Association is an independent, not-for-profit membership organization, headquartered in Geneva, Switzerland.



## Code

github: [`libra`](https://github.com/libra)

- Libra Core (Testnet Client), github: [`libra/libra`](https://github.com/libra/libra)

### Blockchain / Protocol

- [The Official Libra Blockchain / Protocol White Paper - Web Version](https://developers.libra.org/docs/the-libra-blockchain-paper), [PDF Download (~400k, 29 Pages)](libra-blockchain-whitepaper.pdf) 

> **Abstract**: The Libra Blockchain is a decentralized, programmable database designed to support a
> low-volatility cryptocurrency that will have the ability to serve as an efficient medium of exchange for
> billions of people around the world. We present a proposal for the Libra protocol, which implements
> the Libra Blockchain and aims to create a financial infrastructure that can foster innovation, lower
> barriers to entry, and improve access to financial services. To validate the design of the Libra protocol,
> we have built an open-source prototype implementation - Libra Core - in anticipation of a global
> collaborative effort to advance this new ecosystem.
>
> The Libra protocol allows a set of replicas - referred to as validators - from different authorities
> to jointly maintain a database of programmable resources. These resources are owned by different
> user accounts authenticated by public key cryptography and adhere to custom rules specified by the
> developers of these resources. Validators process transactions and interact with each other to reach
> consensus on the state of the database. Transactions are based on predefined and, in future versions,
> user-defined smart contracts in a new programming language called Move.
>
> We use Move to define the core mechanisms of the blockchain, such as the currency and validator
> membership. These core mechanisms enable the creation of a unique governance mechanism that
> builds on the stability and reputation of existing institutions in the early days but transitions to a
> fully open system over time.

### Move Programming Language

_The (Secure) Contract-Oriented Programming Language for Digital (Blockchain) Resources / Assets_

- [The Official Move: A Language With Programmable Resources White Paper - Web Version](https://developers.libra.org/docs/move-paper), [PDF Download (~200k, 26 Pages)](move-whitepaper.pdf) 

> **Abstract:** We present Move, a safe and flexible programming language for the Libra Blockchain.
> Move is an executable bytecode language used to implement custom transactions and smart contracts.
> The key feature of Move is the ability to define custom resource types with semantics inspired by linear
> logic: a resource can never be copied or implicitly discarded, only moved between program storage
> locations. These safety guarantees are enforced statically by Move's type system. Despite these
> special protections, resources are ordinary program values - they can be stored in data structures,
> passed as arguments to procedures, and so on. First-class resources are a very general concept that
> programmers can use not only to implement safe digital assets but also to write correct business
> logic for wrapping assets and enforcing access control policies. The safety and expressivity of Move
> have enabled us to implement significant parts of the Libra protocol in Move, including Libra coin,
> transaction processing, and validator management.


Planned to be a statically-typed programming language derived from Rust, compiled to bytecode.
Example of a peer-to-peer transaction script:

```
// Simple peer-peer payment example.

// Use LibraAccount module published on the blockchain at account address
// 0x0...0 (with 64 zeroes). 0x0 is shorthand that the IR pads out to
// 256 bits (64 digits) by adding leading zeroes.
import 0x0.LibraAccount;
import 0x0.LibraCoin;
main(payee: address, amount: u64) {
  // The bytecode (and consequently, the IR) has typed locals.  The scope of
  // each local is the entire procedure. All local variable declarations must
  // be at the beginning of the procedure. Declaration and initialization of
  // variables are separate operations, but the bytecode verifier will prevent
  // any attempt to use an uninitialized variable.
  let coin: R#LibraCoin.T;
  // The R# part of the type above is one of two *kind annotation* R# and V#
  // (shorthand for "Resource" and "unrestricted Value"). These annotations
  // must match the kind of the type declaration (e.g., does the LibraCoin
  // module declare `resource T` or `struct T`?).

  // Acquire a LibraCoin.T resource with value `amount` from the sender's
  // account.  This will fail if the sender's balance is less than `amount`.
  coin = LibraAccount.withdraw_from_sender(move(amount));
  // Move the LibraCoin.T resource into the account of `payee`. If there is no
  // account at the address `payee`, this step will fail
  LibraAccount.deposit(move(payee), move(coin));

  // Every procedure must end in a `return`. The IR compiler is very literal:
  // it directly translates the source it is given. It will not do fancy
  // things like inserting missing `return`s.
  return;
}
```

## Consensus with Byzantine Fault Tolerance (BFT)

_Inside Libra Byzantine Fault Tolerance (BFT) and the HotStuff Protocol - The Truth Machine with State Replication_ 


- [The Official Libra Byzantine Fault Tolerance (BFT): State Machine Replication in the Blockchain White Paper - Web Version](https://developers.libra.org/docs/state-machine-replication-paper), [PDF Download (~300k, 41 Pages)](libra-consensus-whitepaper.pdf) 

> **Abstract**: This report presents LibraBFT, a robust and efficient state machine replication system designed for the Libra Blockchain.
> LibraBFT is based on HotStuff, a recent protocol that leverages several decades of scientific advances in Byzantine fault tolerance (BFT)
> and achieves the strong scalability and security properties required by internet settings. LibraBFT further refines the HotStuff
> protocol to introduce explicit liveness mechanisms and provides a concrete latency analysis. To drive the integration with the Libra
> Blockchain, this document provides specifications extracted from a fully-functional simulator. These specifications include state
> replication interfaces and a communication framework for data transfer and state synchronization among participants. 
> Finally, this report provides a formal safety proof that induces criteria to detect misbehavior of BFT nodes, 
> coupled with a simple reward and punishment mechanism.


- [What is HotStuff and why is it a big deal](https://medium.com/@cypherium/what-is-hotstuff-and-why-is-it-a-big-deal-213f39696763) by Cypherium, June 18, 2019


## Community

- Libra Discussion Forum [`community.libra.org`](https://community.libra.org)
- Libra Dev / Tech Updates [`@libradev`](https://twitter.com/libradev)
- Libra Dev / Tech Blog [`developers.libra.org/blog`](https://developers.libra.org/blog/)


## Testnet

- [Libra Testnet Explorer](https://librabrowser.io) by [Gal Diskin](https://github.com/Disk1n)
- [Libra gRPC](https://github.com/bonustrack/libra-grpc) by Fabien - A lightweight JavaScript library for Libra
- [Connecting to Libra TestNet on Windows with the Windows Subsystem for Linux (WSL)](https://medium.com/coinmonks/connecting-to-libra-testnet-on-windows-with-wsl-45bdfd23150a) by Ibraheem Kolawole Bello, June 19th, 2019


<!-- break -->

- [Help & Discussion in Testnet Channel / Category](https://community.libra.org/c/testnet) @ Libra Discussion Forum


## Timeline

- June 18th, 2019 - Libra Testnet Live and Open Source Libra Client (Core) Code



## Reference

- [Libra (cryptocurrency) @ Wikipedia](https://en.wikipedia.org/wiki/Libra_(cryptocurrency))
  - [Libra (cryptocurrency) Talk / Discussion @ Wikipedia](https://en.wikipedia.org/wiki/Talk:Libra_(cryptocurrency))


## Articles

Official

- [Libra: The Path Forward](https://developers.libra.org/blog/2019/06/18/the-path-forward) by Libra Engineering Team, June 18, 2019 - Today we are announcing the Libra testnet, a live demonstration of an early prototype of the technology behind Libra - a simple global currency and financial infrastructure that can empower billions of people...


Neutral

- [First Look: Libra](https://info.binance.com/en/research/marketresearch/libra.html) by Binance Research, June 18th 2019 - An in-depth review of Facebook's long-anticipated entry into cryptocurrency


Skeptic / Critical

- [Facebook's Libra Must Be Stopped](https://www.project-syndicate.org/commentary/facebook-libra-must-be-stopped-by-katharina-pistor-2019-06) by Katharian Pistor (Project Syndicate), June 20, 2019 - Facebook has now unveiled a cryptocurrency and payment system that could take down the entire global economy. Governments must intervene before a company that "moves fast and breaks things" ends up breaking everything...

- [Facebook's Cryptocurrency: Stop It Before It Starts](https://www.lawfareblog.com/facebooks-cryptocurrency-stop-it-it-starts) by Nicholas Weaver, June 19, 2019 - The [Libra] coin is really a Facebook project. It is not live yet, giving governments the opportunity to kill this project before it gets off the ground and gives rise to cybercriminals who couldn't capitalize on existing cryptocurrencies. In particular, the [Internal Revenue Service] (IRS) and [the U.S. Treasury's Financial Crimes and Enforcement Network] (FinCEN) should take action now...

- [FacebookCoin is being announced on Tuesday - and we still don't know why it's a crypto](https://davidgerard.co.uk/blockchain/2019/06/15/facebookcoin-is-being-announced-on-tuesday-and-we-still-dont-know-why-its-a-crypto/) by David Gerard, June 15, 2019 - FacebookCoin is being announced on Tuesday! It's called Libra, and Facebook are apparently stressing very hard that everyone should call it Libra, and not Facebook-anything. And definitely not ZuckerBucks...



## Articles (de)

- [Facebooks Zuckerbucks: Es hätte schlimmer kommen können](https://bitcoinblog.de/2019/06/18/facebooks-zuckerbucks-es-haette-schlimmer-kommen-koennen/) by Christoph Bergmann (BitcoinBlog.de), June 18, 2019
- [Facebooks GlobalCoin: Gedeckt durch einem Korb anderer Währungen](https://bitcoinblog.de/2019/06/12/facebooks-globalcoin-gedeckt-durch-einem-korb-anderer-waehrungen/) by Christoph Bergmann (BitcoinBlog.de), June 12, 2019


## Awesome Awesomeness

_A curated list of awesome lists_

- [Awesome Libra Blockchain](https://github.com/IrKor/awesome-libra) by Irina 


## Meta

**License**

![](https://publicdomainworks.github.io/buttons/zero88x31.png)

The list is dedicated to the public domain. Use it as you please with no restrictions whatsoever.

