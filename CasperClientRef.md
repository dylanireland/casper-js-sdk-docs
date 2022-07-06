## `CasperClient` Methods

* ### `makeDeploy`

The `makeDeploy` function allows you to create a valid deploy payload by passing in the following objects:

* `deployParams: DeployParams`
* `session: ExecutableDeployItem`
* `payment: ExecutableDeployItem`

Example:

```typescript
const client: CasperClient = CasperClient("http://node_ip_address:7777/rpc")

const publicKey = CLPublicKey.fromHex("01eca8d440ab343dc73b0b2c1c0892ca08f067a98753532de6e99f5945fecdc2c6")
const chainName: string = "casper" //For mainnet, use "casper". For testnet use "casper-test".

const deployParams: DeployParams = DeployParams(publicKey, chainName)
const session: ExecutableDeployItem = "" //idk atm
const payment: ExecutableDeployItem = DeployUtil.standardPayment(1000000000) //1 CSPR

const unsignedDeploy: Deploy = client.makeDeploy(deployParams, session, payment)
```

* ### `signDeploy`

The `signDeploy` function signs a valid `Deploy` object by an account. It requires the following objects as arguments:

* `deploy: Deploy`
* `signingKey: AsymmetricKey`

Example:

```typescript
const client: CasperClient = CasperClient("http://node_ip_address:7777/rpc")

const publicKey: CLPublicKey = CLPublicKey.fromHex("01eca8d440ab343dc73b0b2c1c0892ca08f067a98753532de6e99f5945fecdc2c6")
const privateKey: Uint8Array = ByteConverters.toBytesString("302e020100300506032b657004220420719421b838bca9148464cc2eb5fd2a7fbd3db1da2ed7b9c418b123e364a544cb")
const signingKey: AsymmetricKey = AsymmetricKey(publicKey, privateKey, SignatureAlgorithm('ed25519'))

const signedDeploy: Deploy = client.signDeploy(unsignedDeploy, signingKey)
```

* ### `putDeploy`

You may submit a deploy to the node addressed in your `CasperClient` instantiation by using `putDeploy`, you will need to pass in a signed deploy object.

Example:

```typescript
const client: CasperClient = CasperClient("http://node_ip_address:7777/rpc")
var deployHash: string
client.putDeploy(signedDeploy).then((result) => {
  deployHash = result
}).catch((error) => {
  console.log(error)
})
```

* ### `deployToJson`

You may use `deployToJson` to convert a `Deploy` object to JavaScript JSON. This can be useful for sending a deploy to the front-end to be signed by the Casper Signer. Using this method is simple, simply call `deployToJson` on your `CasperClient` instance, passing in a valid unsigned `Deploy`.

```typescript
const client: CasperClient = CasperClient("http://node_ip_address:7777/rpc")

const jsonDeploy = client.deployToJson(unsignedDeploy)
```

* ### `deployFromJson`

You may use `deployFromJson` to convert a JavaScript JSON object to a `Deploy`  object . This can be useful for converting a JSON deploy, coming from the Casper Signer, into a usable `Deploy` instance for submission to a node. Using this method is simple, simply call `deployFromJson` on your `CasperClient` instance, passing in a valid JSON deploy.

```typescript
const client: CasperClient = CasperClient("http://node_ip_address:7777/rpc")

const jsonDeploy = client.deployFromJson(jsonDeploy)
```

