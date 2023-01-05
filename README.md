# Graphite Web3 Documentation

# Basic Usage

- Install the GraphiteWallet Extension
- Detect the provider (`window.graphitewallet.ethereum`)
- Call the `eth_requestAccounts` method
- If access is granted, use the available api

# Available methods

### eth_requestAccounts: Promise\<Array>

Calling this method triggers a user interface that allows the user to approve or reject account access for a given dapp.
This method returns a Promise that is resolved with an account if the user approves access or rejected with an Error if
the user rejects access.

```js
const ethereum = window.graphitewallet.ethereum

ethereum
  .request({method: "eth_requestAccounts"})
  .then(account => {
    // account = ["0x3526e99937b60e2cc18f10f6262b4d320fd2e371"]
  })
  .catch(error => {
    // If regected:
    // {code: -1, message: "User rejected the request."}
  })
```

### eth_getBalance: Promise\<string>

Returns the total available account balance.

```js
const account = await ethereum.request({method: "eth_accounts"})
const address = account[0]

ethereum
  .request({
    method: "eth_getBalance", params: [address, "latest"]
  })
  .then(balance => {
    // balance = 0x717aa6d304b2f2
  })
```

### ethereum.request(): Promise\<Object>>

You can access the Ethereum node through the request method. For example:

```js
ethereum
  .request({
    method: "eth_getTransactionCount",
    params: [address, "latest"]
  })
  .then(count => {
    // count = 0x9f
  })

ethereum
  .request({method: "eth_blockNumber", params: []})
  .then(number => {
    // number = 0xf25b65
  })
```

If the request returns an error, you will receive a message like this:
// {code: -3, message: "Invalid jsonrpc request.", details: {code:-32000, message:"already known"}}

### personal_sign: Promise\<string>

You can sign the message with your private key using the `personal_sign` method. The extension will pop up the sign
window.

```js
const account = await ethereum.request({method: "eth_accounts"})
const address = account[0]
ethereum
  .request({method: "personal_sign", params: ["Hello", address]})
  .then(data => {
    // data = 0xc5e86fa7706dcb3386431b050aadda2df8f2db7f6557f5cf85b2a562e6c2683d6a3b0e9fa612a4eeb7d30bf08168ed61bc1c901867f7b3919080b5308437e2001b
  })
```

### eth_signTransaction: Promise\<string>

Signs a transaction that can be submitted to the network at a later time using with `eth_sendRawTransaction`. The extension will pop up the sign window.
**Parameters:**

- from: DATA, 20 Bytes - (optional, the current account address is used) The address the transaction is sent from.
- to: DATA, 20 Bytes - (required) The address the transaction is directed to.
- gas: QUANTITY - (optional, default: 21000) Integer of the gas provided for the transaction execution. It will return unused gas.
- gasPrice: QUANTITY - (required) Integer of the gasPrice used for each paid gas.
- value: QUANTITY - (optional) Integer of the value sent with this transaction.
- data: DATA - (optional) The compiled code of a contract OR the hash of the invoked method signature and encoded parameters.
```js
const transactionParameters = {
  "to": "0xca8a66887dfbef870a2d96de536986516a37fa12",
  "value": "0xad78ebc5ac6200000",
  "gasPrice": "0x2678011db",
  "gas": "0x5208"
}
const signedTx = await ethereum.request({
  method: "eth_signTransaction",
  params: [transactionParameters],
})
// signedTx = 0xf86c819f8502678011db82520894ca8a66887dfbef870a2d96de536986516a37fa1287071afd498d00008026a0646cc12fa4189135f824dd0c748a09dd6583a35e3c06956ea6ffa136ebedd1dea02011331fac8499a02004207fe89d7ce9aa710f91621b9391cebee9b853cf4f57

const hash = await ethereum.request({
  method: "eth_sendRawTransaction",
  params: [signedTx]
})

// hash = 0xfd6e561103ad1391db778ce7c4bb7a9a565e3278b35ad3c604bb20ff8033de94
```

### eth_sendTransaction: Promise\<string>

If you want to sign and send the transaction immediately, then use the `eth_sendTransaction` method. The extension will pop up the sign window.
Parameters are the same as in `eth_signTransaction` method.
```js
// Example token parameters
const tokenParams = {
  "to": "0xdac17f958d2ee523a2206206994597c13d831ec7",
  "nonce": "0x9e",
  "value": "0x0",
  "gasPrice": "0x2a8f4f470",
  "gas": "0x10680",
  "from": "0x3526e99937b60e2cc18f10f6262b4d320fd2e371",
  "data": "0xa9059cbb000000000000000000000000ca8a66887dfbef870a2d96de536986516a37fa1200000000000000000000000000000000000000000000000000000000004c4b40"
}

const hash = await ethereum.request({
  method: "eth_sendTransaction",
  params: [tokenParams],
})
// hash = 0xc292eb8527e36efd918b815bac0c1cd04d7c457043105fa4946e515d8e117b68
```
