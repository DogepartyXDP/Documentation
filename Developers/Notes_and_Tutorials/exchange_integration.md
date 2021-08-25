# Dogeparty Exchange Integration

By adding support for Dogeparty, your exchange not only gets [XDP market](http://coinmarketcap.com/currencies/dogeparty/) support, but support for any other Dogeparty assets listed [here](http://dogeparty.xchain.io/assets)).

Technically, the process is rather straightforward. However, as Dogeparty is not a fork of Dogecoin Core, adding Dogeparty support to your exchange is slightly different from adding support for a cryptocurrency that is, like Litecoin or Dogecoin.  We outline the general process below (for XDP, but the process is identical for all Dogeparty assets):

## Basic Setup

- Follow the instructions [here](https://github.com/DogepartyXDP/Documentation/blob/master/Installation/dogeparty_node.md) to set up a full node. You can install the `base` configuration (e.g. `dogenode install base master`)

- Once the system is set up, get started working with `dogeparty-server`'s [API](https://github.com/DogepartyXDP/Documentation/blob/master/Developers/API.md).


## Handling Deposits using Separate Addresses

- Create a XDP holding address (or several primary XDP holding addresses). The address will hold deposited XDP funds for all users using the exchange.

- Create a regular Dogecoin address for each user wanting to deposit XDP using the API of the Dogecoin Core instance that `dogeparty-server` is connecting to.

- Poll for deposits using `get_sends` [API method](https://github.com/DogepartyXDP/Documentation/blob/master/Developers/API.md), filtering for `asset==XDP`, `destination==deposit_address` and `block_index<=current_block_index-number_of_desired_confirmations`. Record the quantity of the send transaction and the transaction's `txid`.

- 'Prime' the deposit address by sending it 0.0005 BTC.

- For deposit, send the quantity deposited to the holding address using the `do_send` [API method](https://github.com/DogepartyXDP/Documentation/blob/master/Developers/API.md) with the flag `unconfirmed=True` (so you don't have to wait for the priming to confirm). Record the `txid` of this transaction.

- When the second send is confirmed (poll `get_sends` again), credit the user’s account balance.


## Handling Deposits using Memo Transactions

- Create a XDP deposit address. The address will hold deposited XDP funds for all users using the exchange.

- 'Prime' the deposit address by sending it 0.001 BTC.

- Make the deposit address require a memo by [broadcasting](https://github.com/DogepartyXDP/Documentation/blob/master/Developers/API.md#create_broadcast) `OPTIONS 1` from that address.  The value and fee_fraction can be 0.

- When a user wishes to deposit to your exchange, generate a unique hexadecimal invoice ID for the deposit and convey that to the user.  The user must send dogeparty assets into the address along with the matching invoice ID in the memo field.  If the user fails to include a memo, the send will be rejected by the network and the user's address will retain the assets they sent.

- Poll for deposits using `get_sends` [API method](https://github.com/DogepartyXDP/Documentation/blob/master/Developers/API.md), filtering for `asset==XDP`, `destination==deposit_address` and `block_index<={current_block_index-number_of_desired_confirmations}` and `memo_hex=={invoice_id}`. Record the quantity of the send transaction and the transaction's `txid`.

- When the send is confirmed with 2 confirmations (poll `get_sends` again), credit the user’s account balance.

- Memo transactions are available as of block 489956

## Handling Withdrawals (Single Send)

- Prime the holding address if its current balance is below 0.0005 BTC.

- Send the funds to the user-provided address with `create_send` (Dogeparty API).

## Batching Withdrawals (Multi-Peer-Multi-Asset Send)

* This multi-send feature is not yet supported until dogecoin core supports segregated witness support*
- Prime the holding address if its current balance is below 0.0005 BTC.

- Generate first MPMA transaction by making a `create_send` (Dogeparty API) call and specify as many assets and recipient addresses as you would like.

- Sign and Broadcast first MPMA transaction and note `txid`

- Generate second MPMA transaction by making identical `create_send` (Dogeparty API) call as before, except also specify `p2sh_pretx_txid` param and give `txid` of first MPMA transaction

- Sign and Broadcast second MPMA transaction

## Best practices

- For deposits, wait for at least two confirmations on the send to the desposit address and one confirmation for the send to the holding address.

- Keep the private key for the holding address secret and safe.

- Keep the bulk of your exchange's funds in cold storage.

- Set a maximum XDP and BTC withdrawal amount, both per day and per event.

- Use a segwit address for memo deposits and MPMA/Batched withdrawals to keep transaction costs minimal.