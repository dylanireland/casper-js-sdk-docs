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

Next instantiate your Casper client, and define the network name, payment amount (in motes), transfer amount (in motes), and transfer id (nonce).

```typescript
const client: CasperClient = new CasperClient("http://node_ip_address:7777/rpc")

const networkName = "casper-test" //For mainnet use "casper"
const paymentAmount = 10000000000 //10 CSPR
const transferAmount = 10000000000 //10 CSPR
const transferId = 1657139524
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

Now create and sign the deploy with `makeDeploy` and `signDeploy`:

```typescript
let deploy = DeployUtil.makeDeploy(deployParams, session, payment)
deploy = DeployUtil.signDeploy(deploy, senderKey)
```

Lastly, deploy your `Deploy` to the Casper Network:

```typescript
var deployHash: string
client.putDeploy(signedDeploy).then((result) => {
  deployHash = result
}).catch((error) => {
  console.log(error)
})
```

## Installing Smart Contracts

It is quite easy to install smart contracts using the Casper JS SDK. This section will show you how to install WebAssembly smart contracts to the Casper Network.

Start by instantiating a `CasperClient` instance with the node you will deploy your smart contract to. Smart contracts are stored in the global state, but you must select a node to deploy to, and it will from there gossip your transaction to its peers.

```typescript
const client: CasperClient = new CasperClient("http://node_ip_address:7777/rpc")
```

Then create a `Contract` instance, passing in your `CasperClient` object.

```typescript
const contract = new Contracts.Contract(client)
```

Create references to your signing keys. You will need an `AsymmetricKey` representation for the upcoming functions. Here is an example using a `pem` file stored locally:

```typescript
const keys: AsymmetricKey = Keys.Ed25519.loadKeyPairFromPrivateFile("./secret_key.pem")
```

For this guide we'll be using the [highscore.rs](https://github.com/casper-ecosystem/rpc-workshop-1/blob/main/contract/contract/src/highscore.rs) smart contract, that stores the theoretical score of a user in a game alongside the user's public key. Feel free to clone, compile, and install this contract to follow along with this guide.

Load in your smart contract compiled as a `.wasm` binary. In this example, we're using the npm package `fs` to easily load in the file. We'll need the full buffer, and then have to cast it to a `Uint8Array`.

```typescript
try {
  wasm = new Uint8Array(fs.readFileSync('highscore.wasm').buffer)
} catch (error) {
  console.error(error)
}
```

Use the `install` method on your `Contract` object to create an installment deploy.

```typescript
const max_gas_motes = "30000000000" //30 CSPR
const chainName = "casper-test" //Use "casper" for mainnet

const deploy = contract.install(
  wasm,
  RuntimeArgs.fromMap({}), //Empty RuntimeArgs
  max_gas_motes,
  keys.publicKey,
  chainName,
  [keys]
)
```

Call `putDeploy` on your `CasperClient` object to install the contract to the network. This deploy is signed by the provided keys in `contract.install`.

```typescript
try {
  deployHash = await client.putDeploy(deploy)
  console.log(deployHash)
} catch(error) {
  console.error(error)
}
```

To query the status of your deploy, you may use the `getDeploy` method within `CasperClient`.

```typescript
try {
  response = await client.getDeploy(deployHash)
  console.log(response)
} catch(error) {
  console.error(error)
}
```

You can also watch and await the execution of the deploy by visiting [testnet.cspr.live](https://testnet.cspr.live) or [cspr.live](https://cspr.live) for the mainnet, and searching the deploy hash.

By altering our `getDeploy` to poll every few seconds, we can wait until our deployment is executed and acquire the smart contract hash. This is also possible by listening to the event stream, but that is out of scope for this guide.

```typescript
const interval = 2000 //milliseconds

var poll = setInterval(async function(deployHash) {
  try {
    response = await client.getDeploy(deployHash)
    if (response[1].execution_results.length != 0) {
      //Deploy executed
      if (response[1].execution_results[0].result.Failure != null) {
       	console.error("Deployment failed")
        clearInterval(poll)
        return
      }
    const contractHash = iterateTransforms(response[1].execution_results[0].result.Success.effect.transforms) //Next example
    console.log(contractHash) //The highscore contract hash
    clearInterval(poll)
  } catch(error) {
    console.error(error)
  }
}, interval, deployHash)

```

Now we'll create a function for iterating the list of transforms and finding the `WriteContract` state transformation.

```typescript
function iterateTransforms(transforms) {
  for (var i = 0; i < transforms.length; i++) {
    if (transforms[i].transform == "WriteContract") {
      return transforms[i].key
    }
  }
}
```

From here, we can connect this contract to our `Contract` object.

```typescript
contract.setContractHash(contractHash)
```

## Calling Smart Contract Entrypoints

For this section, we will use the `highscore.rs` contract we installed in the previous example. Once the smart contract installment is complete, it will be stored on the blockchain indefinitely. Our highscore contract has a single accessible entrypoint named `add_highscore` which allows us to set a new highscore for an account as long as it is higher than the last recorded score for that account. The following code samples will guide you through connecting to the existing smart contract, interacting with its entrypoint, and querying highscore data.

Within the `Contract` class is the method `callEntrypoint` which will allow us to create and sign a deploy that will call the `add_highscore` entrypoint.

```typescript
const entrypoint = "add_highscore"
const highscore = 100
const args = RuntimeArgs.fromMap({ "score": CLValueBuilder.u512(highscore) })
const sender = keys.publicKey
const chainName = "casper-test" //Use "casper" for mainnet
const max_gas_motes = "2000000000" //2 CSPR

const entrypointCallDeploy = contract.callEntrypoint(
  entrypoint,
  args,
  sender,
  chainName,
  max_gas_motes,
  [keys]
)
```

Then, like earlier, we may use `putDeploy` from the `CasperClient` class to deploy this deploy to the network.

```typescript
try {
  entrypointCallDeployHash = await client.putDeploy(entrypointCallDeploy)
  console.log(entrypointCallDeployHash)
} catch(error) {
  console.error(error)
}
```

You can query deploy status like earlier as well, this time checking only for success and failure:

```typescript
const interval = 2000 //milliseconds

var poll = setInterval(async function(entrypointCallDeployHash) {
  try {
    response = await client.getDeploy(entrypointCallDeployHash)
    if (response[1].execution_results.length != 0) {
      //Deploy executed
      if (response[1].execution_results[0].result.Success != null) {
       	console.error("Error")
      } else {
        console.log("Success")
      }
      clearInterval(poll)
    } catch(error) {
      console.error(error)
    }
}, interval, deployHash)
```

Next, learn how to query on-chain data in the [informational endpoints documentation]().
