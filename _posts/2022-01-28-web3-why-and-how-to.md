---
layout: post
title: "Web3 — Why and How-to"
published: true
comments: true
categories:
    - technical
tags:
    - web3
    - crypto
    - blockchain
---

# Web3 — Why and How-to

*Originally published on [Medium](https://medium.com/@kunallimaye/web3-why-and-how-to-e20bdb4fffaa), 28 January 2022.*

I have been following the discussions around Web3 with genuine interest. The recent holiday season allowed me time to do a bit of reading on it — so here is a summary of that study.

Spoiler alert — I am optimistic about Web3. And this is purely based on the study of why Web3. I will share more observations once I get to test how the Web3 technologies work in practice, which could potentially change my mind.

Web3 as a term was coined by Gavin Wood, referring to it as a "decentralised online ecosystem based on blockchain".

Allow me to use an example to illustrate how this eco-system would function. It's the new year and your new year's resolution is to lead a healthy lifestyle. You have decided to reduce meat consumption - no more than two times in a week, have at least two serves of veggies per meal, and treat yourself to an ice cream or a sneaky beer only if you have exercised three times that week. Now imagine this *resolution* being *digitised* (commonly known as "contract" or "smart contract") and is made available on your watch or phone. So every time you pay for groceries, food or meals at a restaurant you will get immediate feedback in terms of how does that transaction impact on your *resolution*. Obviously the assumption here is all the involved vendors (green grocers, butchers, restaurants, ice cream shops, gymnasiums, etc) are operating within a Web3 construct.

Questions such as how would you buy food for someone else or should ice cream shops refuse to sell you that second ice cream require further discussion and are not the focus for us today. The important take away here is that the required checks and balances are built into the system. For example, you will get pinged if you try to buy an ice cream at two different shops during the same week. So it's not about whether or not you can afford to buy more than one ice cream but that once the ice cream *ticket* for the week is clipped, No Ice Cream For You!

Looking further along, you could envisage each food item providing a score for its carbon footprint. This score itself would be auditable on a Web3 system. And there you have your next year's resolution of living a greener lifestyle.

## Why

First impressions might be that a common *integration standards* based approach could solve for this. Sure, open standards will facilitate easier integration amongst vendors, but without a shared book of business, the systems across vendors won't be able to *validate* our daily transactions in realtime.

Consider a simple example of double-spending. Unless all the ice cream shops use *a single system*, I could buy an ice cream from shop1 and before that transaction reconciles with the system used by shop2, I could buy an ice cream at shop2.

This *single system* is a Web3 application (also known as *dApp*), which uses blockchain for two purposes:

1. As a shared data store for information
2. To execute arbitrary piece of a software code (smart contract), which itself is stored on a blockchain

Assume for now that the system preserves user privacy and ensures integrity of data — we will cover these important topics in the next section.

This validated and accurate data could be used by medical insurance providers to offer you a hyper-personalised health cover, based on your healthy lifestyle.

## How

Back to our example, and the need for a shared book of business across the two shops. This is where the inherent *decentralised* nature of blockchain (and Web3 by extension) becomes critical. This decentralisation part allows blockchains to deliver four important capabilities required by Web3 eco-systems:

1. **User privacy**: individual transactions on the blockchain identify addresses that participate in a transaction and not the actual *owners* of that address. This *address* is commonly known as a wallet in a Web3 eco-system.
2. **Trustless**: blockchains are known as *trustless* because they take away the notion of trust from user transactions. In this context, think of trust as *blind faith*. Blind faith that everyone involved will do the right thing. Web3 proposes an alternative proof based blockchain system, which can be audited for accuracy and integrity.
3. **Consensus based system**: all transactions are processed based on well defined and pre-agreed rules of the underlying blockchain. Majority of the participants in the eco-system need to validate the transaction before it is finalised. This ensures that a rogue actor won't be able to commit fraud.
4. **Immutable shared data store**: shared data store is simply a distributed data store, which means that data is not *caged-in* by a single vendor/operator. It is easily available for other vendors to participate in operating the blockchain. The immutable nature is just as important. It means that once the state of the transaction (data) is finalised using the consensus mechanism, the only way it can be reversed is via a counter transaction. In our example, number of visits to the gym can't be *edited* to reduce (or increase) by one, but an appropriate new transaction needs to be submitted to the system.

What Web3 is working towards is liberating ***your*** data. The fact that ***you*** have visited a gym or had a beer is *your* data and does not belong to the vendors that provided that service.

Often an argument is made that the skills required to audit, reason and work in this eco system are hard to find. A clear distinction needs to be made between the source of this scarcity.

1. Capability: which could be acquired
2. Access to the system: which traditionally has required various approvals creating barriers for participation.

So the Web3 eco-system is *permission-less*, which doesn't require explicit approvals for participation in the eco-system.

## Where are we today?

As of January-2022, most of the pessimism around Web3 is based on two arguments:

1. Decentralisation: true decentralisation is hard to achieve. Lack of standards, benchmarks, or a scoring system to understand true decentralisation of a system is negatively impacting confidence. There are a few unknowns here like data may not be stored on a decentralised blockchain (e.g. artworks in NFT), who are the true participants involved in consensus decision making.
2. Multiple blockchains: it would be safe to assume that there will be multiple established blockchain systems. Most of the current Web3 apps operate on a targeted set of blockchain(s). True Web3 applications need to perform seamlessly across these multiple blockchains. E.g. A Web3 app should be able to function across Bitcoin and Ethereum networks. At this stage there is limited focus on this cross chain interoperability — Polkadot is an example of ongoing work in this space.

In summary, we have a bit to travel to deliver on the promise of Web3. Incrementalism will get us to Web3 — but what we need is for the established businesses to participate in helping shape the required standards, governance models and streamline pivots that are inevitable along the journey. And dare I say we need established Web3 players to be open to involvement by the established (Web2) businesses. If only VCs are allowed to influence the development of Web3, it is going to take us a fair bit longer to get there.

## Further reading

1. Original Bitcoin [white-paper](https://bitcoin.org/bitcoin.pdf)
2. [Observations](https://moxie.org/2022/01/07/web3-first-impressions.html) by a renowned cryptography engineer, who is not convinced by Web3.
