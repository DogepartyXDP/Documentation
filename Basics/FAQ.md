Frequently Asked Questions
========

[TOC]

### How does Dogeparty work?

Dogeparty embeds data into regular Dogecoin transactions. To a regular Dogecoin client, these transactions look like normal Dogecoin transactions. A Dogeparty node (which runs the Dogecoin client along with [the Dogeparty client software](https://github.com/DogepartyXDP/dogeparty-lib)) will recognize and interpret the data in these Dogecoin transactions based on specific rules. From this, it constructs its own ledger of Dogeparty transactions that it has seen on the Dogecoin network.

To better help understand this, [here](https://dogeparty.xchain.io/tx/3515b1da00d86731c5e38a30176ca464bbcba785bc11155afa6c748a155bd218) is a record of a Dogeparty transaction where one address is sending 1 JDOG token to another address. [Here](https://chain.so/tx/DOGE/3515b1da00d86731c5e38a30176ca464bbcba785bc11155afa6c748a155bd218) is what this transaction looks like to chain.so, a popular Dogecoin block explorer. You can see that while it is indeed a Dogecoin transaction, the amount of Dogecoin moved is small. In reality, the DOGE spent is just enough to compensate the Dogecoin miners to include the transaction in a block. Essentially, the user that sent the transaction is paying the Dogecoin network to record and secure this embedded Dogeparty data.

### So Dogeparty is not its own Blockchain, but "rides on top of" Dogecoin?

Yes. Another way to think of it is similar to a [Russian nesting doll](https://en.wikipedia.org/wiki/Matryoshka_doll), where the bigger doll would be the Dogecoin transaction, and the next doll (inside of it) would be a Dogeparty transaction.

This embedding method is technically known as **embedded consensus**.

### Is Dogeparty "polluting" the Dogecoin blockchain, then?

No. 99%+ of Dogeparty transactions utilize a data encoding method called `OP_RETURN`, which is fully "prunable", meaning that the data may be safely discarded by Dogecoin nodes which wish to do so. For the remaining 1% of transactions, an different encoding method is utilized that produces fully "spendable" outputs. These outputs do not stick around in the critical list of unspent outputs (the "UTXO set").

On top of this, every Dogeparty transaction pays a fair fee to the network for inclusion.

### Are Dogeparty transactions less secure than Dogecoin transactions?

As Dogeparty transactions _are_ Dogecoin transactions, their data is proably just as secure as any other Dogecoin transaction.

### How do the Dogeparty nodes stay in sync? What's to stop one node from disagreeing with another?

As all Dogeparty nodes run the same code, and all receive the same Dogecoin transaction data, the ledgers across each node match exactly. Dogeparty nodes are not like Dogecoin nodes in that they don't communicate with each other: they simply connect to the Dogecoin software and download transactions from it, decoding each one as they go along. In this way, the immense security and computing power behind Dogecoin is leveraged as the "transport network" for Dogeparty data.

Given the above, there is no "Dogeparty peer to peer network" like there is a "Dogecoin peer-to-peer network": Dogeparty-aware nodes comprise a subset of the Dogecoin full nodes in existance.

### What about Sidechains?

Dogeparty is optimal for mainly lower value transactions and greatly benefits from the speed and cost of the main chain. However, if sidechains are ever released for dogecoin, there is no reason that they couldn't be made to work with Dogeparty. This is the beauty of Dogeparty's embedded consensus technology -- it can work with just about any blockchain out there, including sidechain designs.

### What kind of addresses does Dogeparty use?

_Exactly_ the same Dogecoin addresses we all know and love. As such, Dogeparty tokens (such as XDP, JDOG, BACON, and more) may be sent to _any_ Dogecoin address.

### What is XDP?

XDP is the native token of Dogeparty. It is a technical necessity for adding advanced features to Dogeparty, which by nature require a protocol aware currency. Dogecoin can only be aware of DOGE, while Dogeparty can be aware of both DOGE and XDP itself. This makes it possible to escrow funds, trade in a decentralized manner, and harness the full potential of programmable money.

*To learn more about XDP, see [about XDP](FAQ-XDP.md).*

### Can I secure my XDP and tokens in cold storage?

Yes. You can make a regular Dogecoin paper wallet and store them there. Later, you can sweep the funds into a [Dogeparty wallet](https://dogeparty.net/wallets/), like Dogewallet.

### Is a 51% attack against Dogeparty possible?

As every Dogeparty transaction is a Dogecoin transaction, to do a "51% attack" on Dogeparty you would have to do a 51% attack on Dogecoin.

### Besides a 51% attack, what are the other risks to consensus?

The Dogeparty network could be effectively "forked" by a sizable number of people running different versions of the Dogeparty client that had different "consensus sensitive code" (i.e. protocol code). In this case, if a transaction was read in from the Dogecoin client software, the differing code may cause two different interpretations of the data, and thus, two different ledger states.

As long as all participants run software that has the same protocol rules (even if it is different Dogeparty client implementations), this situation will not happen. The reference client includes extensive safeguards that help detect and prevent this from happening.

That being said, [the Dogeparty client](https://github.com/DogepartyXDP/dogeparty-lib) is completely open-source. Anyone is able to copy the code and make their own modifications. They can then run their modified version of the software, which technically may generate a different ledger than everyone else. This is similar to Dogecoin itself. However, to have any impact, that person would have to get others to run it, who would have to trust this individual more than they trust the Dogeparty development team. This new ledger would not be "Dogeparty". It would be a separate ledger with its own protocol rules. Services built on this ledger (such as a block explorer) would not agree with similar services built on the Dogeparty ledger.

### So can the Dogeparty Team rewrite the Dogeparty ledger’s history, in an emergency or by decree? How does that compare to the same risks with Dogecoin Core devs?

It’s identical to the case with Dogecoin. The Dogecoin core devs could publish a copy of Dogecoin Core that does anything, but no one would download it.

Dogeparty is 100% open source, with [a list of code changes](https://github.com/DogepartyXDP/dogeparty-lib/releases) from one release to the next visible for all to see and inspect.

### What about support for other blockchains instead of Dogecoin?

Dogeparty is built on Dogecoin. That has always been the case and we do not see it changing, ever. For other blockchains, there are "forks" of the Dogeparty software. Examples would be Counterparty for Bitcoin, Monaparty for Monacoin, and Dogeparty for Dogecoin. We generally encourage forks on other blockchains, especially if they help contribute back bug fixes and enhancements to the main Dogeparty codebase.

### What is Dogecoin fails or becomes co-opted?

In the event of a catastrophic failure of the Dogecoin network, Dogeparty _does_ have the technical capability of "freezing" balances and migrating to another blockchain, like Litecoin for instance, with relative ease.

### What happens if and when OP_RETURN data is auto-pruned?

Dogeparty only needs some Dogecoin full nodes somewhere to have an unpruned copy of the blockchain. As every Dogeparty full node is also a Dogecoin full node, this is easily done.

### How are blockchain reorganizations ("reorgs") handled by Dogeparty?

Blockchain reorganizations are essentially handled by Dogeparty the same way they are handled by Dogecoin. If the Dogeparty software detects that a reorganization has occurred, it will utilize an internal "undolog" to quickly undo (roll back) transactions up to the point of the chain branching, and then process new transactions on the now-longest chain.

### How can a thin client trustlessly lookup the Dogecoin public address associated with the OSTOCK asset name?

You can use a local copy of the blockchain just fine. The only difference between Dogeparty and Dogecoin here is that Dogeparty doesn’t support SPV. 
