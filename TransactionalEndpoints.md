# Transactional Endpoints of the Casper JS SDK

The Casper JS SDK includes methods that allow developers building on Casper to access transactional endpoints open on nodes. This guide will walk you through making calls to these endpoints to execute transactions on the Casper Blockchain.

The Casper Network supports programmable and addressable smart contracts, which you can access using the JavaScript/TypeScript SDK. There are classes and methods which are outlined below for transacting without the need for complicated JSON RPC request handling.

## Prerequisites

Before reading this guide, follow the [Wallets and Accounts Guide]() to understand creating and managing wallets, accounts, and key-pairs.

## Build and Deploy a Standard Transfer

To transfer CSPR to another account, first get the `AsymmetricKey` of the sender and recipient. The sender's private key is required, while only the public key of the receiving address is required. The following is an example, but not the only way to obtain `AsymmetricKey` typed keys.

```typescript
const senderKey = Keys.Ed25519.new()
const recipientKey = Keys.Ed25519.new()
```

Next define the network name, payment amount (in motes), transfer amount (in motes), and transfer id (nonce).

```typescript
const networkName = "casper" //For testnet use "casper-test"
const paymentAmount = 10000000000 //10 CSPR
const transferAmount = 10000000000 //10 CSPR
const transferId = 34
```

Then create the deploy parameters:

```typescript
let deployParams = new DeployUtil.DeployParams(
    senderKey.publicKey,
    networkName
)
```

Next create the session code that contains the logic for the deploy

```typescript
let session = DeployUtil.ExecutableDeployItem.newTransfer(
    transferAmount,
    recipientKey.publicKey,
    undefined, //Optional source purse URef. Using undefined will default to the account making the request.
    transferId
)
```

You may also instead create the `session` object using `newTransferWithOptionalTransferId` to auto-generate the `transferId`.

```typescript
let session = DeployUtil.ExecutableDeployItem.newTransferWithOptionalTransferId(
    transferAmount,
    recipientKey.publicKey
)
```

Now create and sign the deploy with `makeDeploy`:

```typescript
let deploy = DeployUtil.makeDeploy(deployParams, session, payment)
deploy = DeployUtil.signDeploy(deploy, senderKey)
```

Lastly, deploy your `Deploy` to the Casper Network:

```typescript
const client: CasperClient = CasperClient("http://node_ip_address:7777/rpc")
var deployHash: string
client.putDeploy(signedDeploy).then((result) => {
  deployHash = result
}).catch((error) => {
  console.log(error)
})
```

## Interacting with smart contracts

