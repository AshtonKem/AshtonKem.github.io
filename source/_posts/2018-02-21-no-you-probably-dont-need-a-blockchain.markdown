---
layout: post
title: "No, You Probably Don't Need a Blockchain"
date: 2018-02-21 18:39
comments: true
categories: 
---

# On Bitcoins and Blockchains

Oh Bitcoin, the darling of everyone's economic dreams. Some think it'll destroy the fed, others hope it'll kill off the big banks. Some just think it's going to make them rich, others go as far as hoping it'll kill off governments completely. Whatever you happen to hate, Bitcoin is coming to destroy it!

All of this is complete nonsense, of course. There are enough mutually exclusive dreams about Bitcoin that it's clear that not all of them can come true, possibly even the hopes of easy riches given its unpredictable volatility. 

But even if you don't buy into the dream of buying bitcoins to turn them into Lambos or whatever, chances are you are hearing from every single corner that Bitcoin isn't the future, it's the blockchain! This refrain has been said so many times that even Bitcoin skeptics feel the need to mention that the blockchain will change the world, even if they don't think that Bitcoin itself will. That this refrain becomes louder whenever Bitcoin is dropping vs. USD doesn't seem to worry that many people.

I personally think all of this blockchain stuff is bunk. I am extremely skeptical that the blockchain is indeed the future, and if there will be any future uses for it I don't think we've seen it yet. What we have seen instead is a ton of hype and not a small amount of straight up fraud, which I find worrying.

## A Pesky Disclaimer.

First of all, I am not a financial adviser. If you take any of this as advice as far as what to buy or sell, both of us will be very sad. Well, you probably will be sad, I will be annoyed if you bug me about it. If you want legit financial advise, go find an actual financial adviser and _pay them_.

Secondly, predicting the future is _hard_. While I am confident that I have not seen any uses for the technologies discussed yet, I am not foolish enough to think that there is a 0% chance that one ever will. I will lay out why I think it's unlikely, but only time will tell.

# So, What _is_ a Blockchain Anyways?

A blockchain is a way of recording a series of transactions in a publicly visible and immutable way. Individual transactions are created with private encryption keys, and then bundled into blocks. Each block is then publicly verified against some sort of algorithm, typically one that involves a lot of computational power to get done. Each verified block contains a reference to the previous block, thus creating a chain of blocks, or a blockchain. The idea is that blocks are very expensive to create, but very cheap to check. If you wish to check the balance or history of a given bitcoin address ("wallet"), then you merely need to scroll backwards through all the blocks and check for any transaction that references that address. 

The basic idea is that the verification of any transaction is essentially trustless, there doesn't need to be any central authority to provide the record of any transaction. All you need is a copy of the blockchain in order to verify any past transaction, should you wish to do so. If you wish to do any transaction on the blockchain, all you need is a copy of the private key for a given wallet. These can be stored in a variety of ways in software or hardware. The people verifying blocks will facilitate your transaction without you or them knowing who each other is. 

## So, Why the Talk of Blockchains Instead of Bitcoin? 

Because Bitcoin uses _a_ blockchain, but it isn't _the_ blockchain. A blockchain effectively consists of the rules of the chain, the current chain, and all the participants in it. The chain itself is the combination of the starting block, and the longest accepted chain of transactions. This is why there are so-called altcoins, which are blockchain currencies that use slightly different rules or a different initial starting block from the blockchain.

More interestingly still are cases where sections of a blockchain decide that a specific block was invalid, either for security or political reasons. These create a so-called hard fork, where two competing blockchains exist that share a large section of their history. A good example of this is the ever controversial [Bitcoin Cash](https://en.wikipedia.org/wiki/Bitcoin_Cash), which was created to overcome perceived deficiencies in Bitcoin itself. If you owned Bitcoin before the fork in August of 2017, then you now own an equal amount of Bitcoin and Bitcoin Cash. Whether or not you think this is economically sound is up to you.

# So, Why Don't You Like Blockchains?

I think blockchains are a novel combination of existing technologies, what I am not convinced of is their long term utility. While it's been fun to watch a speculative bubble from around cryptocurrencies and their related companies, to my eyes most attempts to expand the blockchain beyond that bubble strike me as foolish at best.

## Immutability Woes

Blockchains are by their very design immutable. Barring extreme measures, any transaction entered into them is permanent and irreversible. For financial systems this has a lot of obvious drawbacks: fraudulent or mistaken transactions cannot be undone. Ebay purchaser did not deliver? Tough. Someone held you [hostage](https://www.ccn.com/man-held-gunpoint-bitcoin-comes-forward/) until you sent them bitcoin? Tough. Private key hacked, or hard drive destroyed? That's unfortunate for you. The only way to recover bad bitcoin transactions is to convince everyone to do a hard fork, and needless to say that the bar for that is very very high.

But that's just finance, what about industry? It turns out, that there's very few systems that truly benefit from an inability to amend or update transactions. Humans bungle input into computers all the time, there's a whole sector of the economy (customer service) designed to help deal with that. But in an immutable system, it is literally impossible to undo anything written to the blocks. The only possible way to somewhat reverse a bad transaction is to literally make the inverse transaction the next time around, which would involve convincing the opposite party to cooperate. This would rapidly clutter up the chain full of "oops" transactions, which would pretty quickly make you wonder why you're bothering at all.

## That Pesky Real World Thing

One of the most common proposed usages of blockchain is to track the shipment of real world goods, including everything from food to diamonds. On the surface this sounds great, you could easily and trustlessly prove that the shiny rock in your beloved's ring wasn't mined by children in a war torn region, fantastic!

The problem is that computers are not the real world. Currently blockchains have a very hard time affecting even digital systems that are not on the blockchain, which is why centralized exchanges exist to facilitate buying bitcoin with USD/EUR/etc. Actually trying to make computers in general reflect the real world is a very hard task, and the blockchain does nothing to solve this issue.

Imagine that you are a blood diamond distributor. You know that rich westerners love diamonds, but feel a bit bad about children being hurt and killed to mine these conflict diamonds. You have a huge incentive to simply lie to the computer to create a fraudulent paper trail for these diamonds, thus generating a higher market value for them. There's nothing about the blockchain that prevents humans from lying or mistakenly entering the wrong values, and the immutable nature of blockchains makes the cost of mistakes oh so much higher.

Don't bother thinking about secure hardware at verified locations. At its heart blockchains only require a password to operate, as once you have the private key you can create transactions trivially. If media pirates can extract decryption keys from BluRay players, I guarantee you that diamond smugglers will be able to figure this out too. Once the hardware is in the attackers hands, it is only a matter of time until they strip it down to extract the keys.

The only way around this is to have a trusted neutral party verifying that goods come from exactly where they claim to be from. Not only does this fly in the face of the "trustless" idea above, but handing said employee the ability to create immutable "this diamond is legit" records puts that employee in way more danger than need be. There's literally nothing about this system that's superior to what we already have.

## Power & Motivation

Blockchains typically use the "Proof of Work" system, where multiple participants try to solve a difficult problem in order to certify a block to be valid. The problem is hard to prevent one person from controlling too much of the mining (computational) pool. This is one of the reasons why Bitcoin itself currently consumes roughly as much power as Delaware while processing about 4 transactions a second. 

Problem: that much electricity is very expensive, as is all of the specialized hardware required to do it. Those that do the work, miners, are commercial operations and expect to be rewarded for their effort. With Bitcoin and other currencies, this reward is the currency itself. The first miner that solves the problem is rewarded with a few Bitcoins, hence the unkind comparisons to Ponzi or MLM schemes. In any non-monetary application, these miners will need to be compensated to do the work running the blockchain. Given the inefficiency of Proof of Work, this will be orders of magnitude more expensive than a similarly sized centralized solution. 

There are two solutions to this. Offer miners a token of some sort for their work, or run all the hardware yourself. The former is the basis of many ICOs, and most have more than a whiff of fraud about them. The latter is basically just a really crappy traditional system with some fancy terminology and a very expensive power bill.

### Proof of Stake

There's an alternative system to Proof of Work, which is Proof of Stake. In Proof of Stake the person holding the most old currency is selected as the person who will validate the latest block. Rather than each validator getting paid a set amount of new coins, they are paid a fee by everyone hoping to transact in order to validate the block. Rather than keeping the longest chain as the most valid, since the longest chain in Proof of Work is the most mined, the chain with the most old money in it is determined to be valid in Proof of Stake.

Proof of Stake solves some, but not all, of the most egregious problems with the current Proof of Work system. The power and silicon bill would be significantly lower, but not necessarily free. You'd still need to pay people to use their phone/computer/whatever to run transactions on it. The chain would also run more transactions, better than the miserable 4/s on bitcoin, but this would only increase the size of the overall chain. Remember, every transaction _ever_ stays on the blockchain permanently. The Bitcoin blockchain is currently 157GB in size, and that's at a very low transaction speed. The idea of everyone mining on their phone is just laughable. 

Also, Proof of Stake doesn't improve the real world interface or immutability problems above.

## Scale

To be blunt, the performance of basically all blockchain systems today is garbage. The limited number of transactions that can fit into a block, and the amount of work it takes to prove a block means that blockchain systems using Proof of Work limits their maximum throughput to very low numbers. Bitcoin for example can handle on average 3-4 transactions a second, tops. Ethereum does better in the 15-20 a second range. Both pale in comparison to the average 1,667 transactions a second Visa handles per second based on their reported daily volume, with their peak volume probably being higher. To simplify, Visa can handle the transactions of an good sized nation state, bitcoin can handle the transactions of a few Costcos.

I question how many industrial systems are worth spending the electricity on and need a transaction volume in the single digits a second range. Probably not many. 

Note: There are some plans to improve Bitcoin's transaction speed, mostly by ditching the blockchain itself for all but the rarest of cases. Given that these solutions will appear _any day now_ makes me doubt that they're actually viable. These also include a lot of serious safety drawbacks that are not worth getting into here.

## ICOs, Also Fraud

An interesting sub-genre of the cryptocurrency space are Initial Coin Offerings. This is where someone creates a new cryptographic coin representing a stake in some new venture. These coins are sold off to the highest bidder in order to raise funds, and then eventually the coins will be useful and valuable because magic. Needless to say, there is a _ton_ of fraud in the ICO space.

First of all, ICOs are designed to look a lot like Initial Public Offerings (IPOs), except without all those pesky regulations. The fact that many ICOs confer no voting or dividend rights upon the purchaser, and sometimes aren't allowed to be sold according to the contract has yet to dissuade the market. Even the most up and up ICO typically doesn't provide any particular reason why these tokens should be worth anything in future. 

Second, did I mention fraud? It's estimated that up to 10% of the funds put into ICOs has been straight up stolen. Sometimes this is the creator of the ICO collecting bitcoin or whatever for the ICO and never delivering the token, simply replacing their website with [a the word "penis"](http://nymag.com/selectall/2018/01/prodeum-scam-cryptocurrency-for-produce-disappears.html) and laundering the currency. Other times its trivial integer underflow bugs letting someone steal [$800k](https://blog.goodaudience.com/how-800k-evaporated-from-the-powh-coin-ponzi-scheme-overnight-1b025c33b530) from a 4chan backed ICO. Half the time the funds are "stolen", it is unclear whether or not a third party took it, or whether or not it's an exit scam disguised as a hack.

Third, very few of these ICOs have even the slightest hint at being related to crypto or valuable in any meaningful way. I've covered the issue with supply chain blockchains above, but "dating on the blockchain" and "porn on the blockchain" is all meaningless bullshit, especially now that nobody really cares what you view in the privacy of your own home anymore. And that's not even considering the insanity that is backing an ICO with [bananas](https://bananacoin.io/). No, I am not kidding. They claim that they they've raised $3,038,361.90, by the way.

For a good example of the nonsense of ICOs, look into the [pre-mortem](https://www.kerrisdalecap.com/wp-content/uploads/2018/02/Eastman-Kodak-Company-KODK.pdf) of Kodak's ICO. The whole plan is bullshit from top to bottom, and includes a non-trivial amount of insider trading. If the Kodak board isn't arrested within the next year for insider trading I will be genuinely shocked.

# But What About Smart Contracts?

Ethereum is the one interesting outlier of all of the crypto currencies and blockchain technologies. Ethereum provides the user with a simple, if not problematic, programming language. You can generate programs, or "smart contracts" (let it not be said that crypto fans are bad at marketing), and run them on the Ethereum blockchain. You have to pay a small fee in ETH, called "gas" based on the complexity of your program, and the result of the program must be some sort of Ethereum transaction. Thus the value of ETH is nominally backed by the utility of running programs on the Ethereum blockchain itself.

On the face of it, smart contracts are an interesting idea. You could cut a lot of legal and human complexity/overhead out by making contracts that self execute. Most smart contract fans repeat the phrase "code is law" in reference to the above idea. Of course, writing programs that produce permanent financial consequences gives me some pretty serious anxiety, and I would hope that most senior engineers would feel the same. And given the number of high profile and high value hacks on Ethereum contracts over the years, it seems like my fears are well founded. That one of the [hacks](https://www.coindesk.com/understanding-dao-hack-journalists/) was serious enough to trigger a hard fork in Ethereum implies that I'm onto something. It turns out that "code is law" is a popular idea right up until enough people lose their money.

On top of that, as best I can tell a _large_ percentage of Ethereum contracts are literal Ponzi schemes and ICO tokens, the later being easy to bootstrap on top of Ethereum using a protocol called ERC20. This calls into question the long term viability of "smart contracts", given that the main usage for this system so far appears to be fraud and refreshingly honest fraud. 

# Conclusion

No, you probably don't need a blockchain.
