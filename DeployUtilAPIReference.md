# `DeployUtil` Casper TypeScript SDK API Reference

## `DeployUtil`

You may use classes within `DeployUtil` to simplify constructing and sending deploys. The available classes are:

* #### `UniqAddress`

  A `UniqAddress` is an object containing a unique address built using the `transferId` of a `Deploy`.

  * Objects

    * `publicKey: CLPublicKey`
      * The `CLPublicKey` representation of the transacting account.
    * `transferId: BigNumber`
      * A `transferId` nonce.

  * Methods

    * ```typescript
      constructor(publicKey: CLPublicKey, transferId: BigNumberish)
      ```

      * Constructs a `UniqAddress` from the transacting account's `CLPublicKey` and unique `transferId`.

    * ```typescript
      toString(): string
      ```

      * Returns a `string` of the `UniqAddress` in the format "accountHex-transferIdHex".

    * ```typescript
      static fromString(value: string): UniqAddress
      ```

      * Builds a `UniqAddress` from a `string` of the format "accountHex-transferIdHex".

* #### `DeployHeader`

  A `DeployHeader` is the header data of a `Deploy` object that contains information like the transacting account, timestamp, gas price, and other relevent deploy information.

  * Objects

    * `public account: CLPublicKey`
      * The `CLPublicKey` representation of the transacting account.
    * `public timestamp: number`
      * The timestamp at which the deploy was generated
    * `public ttl: number`
      * The amount of time in **seconds?** that the deploy has to live before it is dropped and rejected by validators.
    * `public gasPrice: number`
      * The gas price of the deploy. The Casper Mainnet currently has a static gas price of 1 **what? CSPR?**.
    * `public bodyHash: Uint8Array`
      * The **(insert algorithm here)** hash of the wasm of the deploy.
    * `public dependencies: Uint8Array[]`
      * Zero, one, or many instances of session code that is required to execute before the current deploy.
    * `public chainName: string`
      * The chain for which the deploy should run on. For the Casper mainnet, use the string `casper` and for the testnet, use `casper-testnet`.

  * Methods

    * ```typescript
      constructor(account: CLPublicKey, timestamp: number, ttl: number, gasPrice: number, bodyHash: Uint8Array, dependencies: Uint8Array[], chainName: string)
      ```

      * Constructs a `DeployHeader` object.

    * ```typescript
      public toBytes(): ToBytesResult
      ```

      * Converts `DeployHeader` to `ToBytesResult`.

* #### `DeployHash`

  The `DeployHash` object is a standardized type representing a deploy hash.

  * Methods

    * ```typ
      constructor(private hash: Uint8Array)
      ```

      * Constructs a `DeployHash` object from a `Uint8Array` represented deploy hash.

* #### `Approval`

  The `Approval` class contains a signature and public key of a signer.

  * Objects
    * `signer: string`
      * A hexadecimal representation of the signer's public key
    * `signature: string`
      * A hexadecimal representation of a signer's signature.

* #### `ModuleBytes`

  A `ModuleBytes` object can be passed along in a deploy, which includes session code and arguments.

  * Objects

    * `public moduleBytes: Uint8Array`
      * A `Uint8Array` representation of the session code.
    * `public args: RuntimeArgs`
      * A `RuntimeArgs` object containing the runtime arguments passed through with the deploy.

  * Methods

    * ```typesc
      constructor(moduleBytes: Uint8Array, args: RuntimeArgs)
      ```

      * Constructs a `ModuleBytes` object from the `Uint8Array` typed module bytes and a `RuntimeArgs` object.

    * ```type
      public toBytes(): ToBytesResult
      ```

      * Converts `DeployHeader` to `ToBytesResult`.

* #### `StoredContractByName`

  The `StoredContractByHash` class, when instantiated, represents a smart contract referenced by it's hash.

  * Objects

    * `public tag: number = 2`
      * This is an identifier that other functions may use to recognize that `StoredContractByName` objects are indeed `StoredContractByName`s.
    * `public name: string`
      * The name of the contract.
    * `public entryPoint: string`
      * An entry point of the smart contract.
    * `public args: RuntimeArgs`
      * A `RuntimeArgs` object containing the runtime arguments to be passed along with the deploy.

  * Methods

    * ```typescript
      constructor(name: string, entryPoint: string, args: RuntimeArgs)
      ```

      * Constructs a `StoredContractByHash` object from the name and entry point of the contract, and associated runtime arguments.

    * ```type
      public toBytes(): ToBytesResult
      ```

      * Converts `StoredContractByHash` to `ToBytesResult`.

* #### `StoredContractByHash`

  The `StoredContractByHash` class, when instantiated, represents a smart contract referenced by it's hash.

  * Objects

    * `public tag: number = 1`
      * This is an identifier that other functions may use to recognize that `StoredContractByHash` objects are indeed `StoredContractByHash`'s.
    * `public hash: Uint8Array`
      * The `Uint8Array` typed hash of the smart contract.
    * `public entryPoint: string`
      * An entry point of the smart contract.
    * `public args: RuntimeArgs`
      * A `RuntimeArgs` object containing the runtime arguments to be passed along with the deploy.

  * Methods

    * ```typescript
      constructor(hash: Uint8Array, entryPoint: string, args: RuntimeArgs)
      ```

      * Constructs a `StoredContractByHash` object from the `Uint8Array` typed hash, entry point of the contract, and associated runtime arguments.

    * ```type
      public toBytes(): ToBytesResult
      ```

      * Converts `StoredContractByHash` to `ToBytesResult`.

* #### `StoredVersionedContractByName`

  The `StoredVersionedContractByName` class, when instantiated, represents a versioned smart contract referenced by it's hash.

  * Objects

    * `public tag: number = 4`
      * This is an identifier that other functions may use to recognize that `StoredVersionedContractByName` objects are indeed `StoredVersionedContractByName`.
    * `public name: string`
      * The name of the versioned contract.
    * `public version: number | null`
      * The version of the contract, or `null`.
    * `public entryPoint: string`
      * An entry point of the smart contract.
    * `public args: RuntimeArgs`
      * A `RuntimeArgs` object containing the runtime arguments to be passed along with the deploy.

  * Methods

    * ```typescript
      constructor(name: string, version: number | null, entryPoint: string, args: RuntimeArgs)
      ```

      * Constructs a `StoredVersionedContractByName` object by the name of the contract, version number (or null), entry point of the contract, and associated runtime arguments.

    * ```type
      public toBytes(): ToBytesResult
      ```

      * Converts `StoredVersionedContractByHash` to `ToBytesResult`.

* #### `StoredVersionedContractByHash`

  The `StoredVersionedContractByHash` class, when instantiated, represents a versioned smart contract referenced by it's hash.

  * Objects

    * `public tag: number = 3`
      * This is an identifier that other functions may use to recognize that `StoredVersionedContractByHash` objects are indeed `StoredVersionedContractByHash`.
    * `public hash: Uint8Array`
      * The hash of the versioned contract.
    * `public version: number | null`
      * The version of the contract, or `null`.
    * `public entryPoint: string`
      * An entry point of the smart contract.
    * `public args: RuntimeArgs`
      * A `RuntimeArgs` object containing the runtime arguments to be passed along with the deploy.

  * Methods

    * ```typescript
      constructor(hash: Uint8Array, version: number | null, entryPoint: string, args: RuntimeArgs)
      ```

      * Constructs a `StoredVersionedContractByHash` object by the `Uint8Array` hash of the contract, version number (or null), entry point of the contract, and associated runtime arguments.

    * ```type
      public toBytes(): ToBytesResult
      ```

      * Converts `StoredVersionedContractByHash` to `ToBytesResult`.

* #### `Transfer`

  Represents a transferral deploy. Construct and deploy to execute a standard CSPR transfer.

  * Objects

    * `public tag: number = 5`

      * This is an identifier that other functions may use to recognize that `Transfer` objects are indeed `Transfer`s.

    * `public args: RuntimeArgs`

      Runtime arguments necessary for building the transfer deploy.

      * `amount:u512`
        * Amount to transfer in motes.
      * `target:string` 
        * Target URef of the target purse or the public key of target account. You could generate this public key from `accountHex` by `CLPublicKey.fromHex(hex_public_key)`.
      * `sourcePurse:string`
        * URef of the source purse. If this is omitted, the main purse of the account creating this transfer will be used as the source purse.
      * `id:string`
        * User-defined transfer id.

    * Methods

      * ```type
        constructor(args: RuntimeArgs)
        ```

        * Constructs a `Transfer` deploy object from `RuntimeArgs`.

      * ```type
        public toBytes(): ToBytesResult
        ```

        * Converts `StoredVersionedContractByHash` to `ToBytesResult`.

  * #### `ExecutableDeployItem`

    This class, when instantiated, represents an executable deploy object that can be deployed on-chain. `ModuleBytes`, `StoredContractByHash`, `StoredContractByName`, `StoredVersionedContractByHash`, `StoredVersionedContractByName`, and `Transfer` objects can all be casted as `ExecutableDeployItem`s.

    * Objects

      * `public moduleBytes?: ModuleBytes`
        * Optional `ModuleBytes` object representing the `ExecutableDeployItem` if applicable.
      * `public storedContractByHash?: StoredContractByHash`
        * Optional `StoredContractByHash` object representing the `ExecutableDeployItem` if applicable.
      * `public storedContractByName?: StoredContractByName`
        * Optional `StoredContractByName` object representing the `ExecutableDeployItem` if applicable.
      * `public storedVersionedContractByHash?: StoredVersionedContractByHash`
        * Optional `StoredVersionedContractByHash` object representing the `ExecutableDeployItem` if applicable.
      * `public storedVersionedContractByName?: StoredVersionedContractByName`
        * Optional `StoredVersionedContractByName` object representing the `ExecutableDeployItem` if applicable.
      * `public transfer?: Transfer`
        * Optional `Transfer` object representing the `ExecutableDeployItem` if applicable.

    * Methods

      * ```typescript
        public toBytes(): ToBytesResult
        ```

        * Converts `ExecutableDeployItem` to `ToBytesResult` depending on the `ExecutableDeployItem`s type. Throws an error if it cannot serialize the `ExecutableDeployItem` from its parent type.

      * ```typesc
        public getArgByName(name: string): CLValue | undefined
        ```

        * Gets a `CLValue` typed argument via its name, returns `undefined` if the argument does not exist. Throws an error if it cannot serialize the `ExecutableDeployItem` from its parent type.

      * ```typ
        public setArg(name: string, value: CLValue)
        ```

        * Sets an argument given an argument name and a value typed as  `CLValue`. Throws an error if it cannot serialize the `ExecutableDeployItem` from its parent type.

      * ```typ
        public static fromExecutableDeployItemInternal(item: ExecutableDeployItemInternal)
        ```

        * Builds an `ExecutableDeployItem` from an `ExecutableDeployItemInternal`.The `ExecutableDeployItemInternal` abstract class is inherited by `ModuleBytes`, `StoredContractByHash`, `StoredContractByName`, `StoredVersionedContractByHash`, `StoredVersionedContractByName`, and `Transfer`, so you may pass in an object of any of these types.

      * ```type
        public static newModuleBytes(moduleBytes: Uint8Array, args: RuntimeArgs)
        ```

        * Returns an `ExecutableDeployItem` from a `Uint8Array` typed set of module bytes and corresponding runtime arguments.

      * ```typesc
        public static newStoredContractByHash(hash: Uint8Array, entryPoint: string, args: RuntimeArgs)
        ```

        * Returns an `ExecutableDeployItem` representing a stored contract, referenced by its hash. To use, pass in the smart contract's hash as a `Uint8Array`, the initial entry point of the smart contract, and any runtime arguments.

      * ```typescript
        public static newStoredContractByName(name: string, entryPoint: string, args: RuntimeArgs)
        ```

        - Returns an `ExecutableDeployItem` representing a stored contract, referenced by its name. To use, pass in the smart contract's name, the initial entry point, and any runtime arguments.

      * ```typescript
        public static newStoredVersionContractByHash(hash: Uint8Array, version: number | null, entryPoint: string, args: RuntimeArgs)
        ```

        * Returns an `ExecutableDeployItem` representing a stored contract, referenced by its hash. To use, pass in the smart contract's hash as a `Uint8Array`, the version or null, the initial entry point of the smart contract, and any runtime arguments.

      * ```typescript
        public static newStoredVersionContractByName(name: string, version: number | null, entryPoint: string, args: RuntimeArgs)
        ```

        * Returns an `ExecutableDeployItem` representing a stored, versioned contract, referenced by its name. To use, pass in the smart contract's name, the version or null, the initial entry point, and any runtime arguments.

      * ```typescript
        public static newTransfer(amount: BigNumberish, target: CLURef | CLPublicKey, sourcePurse: CLURef | null = null, id: BigNumberish)
        ```

        * Returns an `ExecutableDeployItem` representing a CSPR transferral deploy. Create a transfer deploy by calling `newTransferWithOptionalTransferId` with the transfer  `amount` in motes, the `target`, which can be a `CLUref` of a purse or a `CLPublicKey` public key of an account, the source purse URef, and the `id` which is commonly the current unix timestamp. If the `sourcePurse` is omitted, it will use the deployers main purse as the token source.

      * ```typ
        public static newTransferWithOptionalTransferId(amount: BigNumberish, target: CLURef | CLPublicKey, sourcePurse?: CLURef | null, id?: BigNumberish)
        ```

        * Returns an `ExecutableDeployItem` representing a CSPR transferral deploy. Create a transfer deploy by calling `newTransfer` with the transfer  `amount` in motes, the `target`, which can be a `CLUref` of a purse or a `CLPublicKey` public key of an account, the source purse URef, and the `id` which is commonly the current unix timestamp. If the `id` is omitted, the SDK will create one for you. If the `sourcePurse` is omitted, it will use the deployers main purse as the token source.

      * ```typescript
        public static newTransferToUniqAddress(source: CLPublicKey, target: UniqAddress, amount: BigNumberish, paymentAmount: BigNumberish, chainName: string, gasPrice = 1, ttl = 1800000, sourcePurse?: CLURef)
        ```

        * Returns an `ExecutableDeployItem` representing a CSPR transferral deploy to a unique address. Create this deploy by calling `newTransferToUniqAddress` with the `source` account as a `CLPublicKey`, the `target` account public key as a `UniqAddress`, the `amount` in motes, the `paymentAmount` in motes, which is your maximum limit to the gas you are willing to pay, the `chainName` (to avoid possibility of deployment on the wrong chain), the `gasPrice`, which if omitted is `1`, the `ttl`, which if omitted is 1800000, and the `sourcePurse` as a `CLUref`. If the `sourcePurse` is omitted, it will use the deployers main purse as the token source.
        
      * ```typescript
        public isModuleBytes(): boolean
        ```
      
        * Returns `true` is the `ExecutableDeployItem` conforms to `ModuleBytes`, and `false` otherwise.
      
      * ```typescript
        public asModuleBytes(): ModuleBytes | undefined
        ```
      
        * Returns the `ModuleBytes` representation of the object, and `undefined` if the data cannot be casted to `ModuleBytes`.
      
      * ```typescript
        public isStoredContractByHash(): boolean
        ```
      
        * Returns `true` is the `ExecutableDeployItem` represents a `StoredContractByHash`, and `false` otherwise.
      
      * ```typescript
        public asStoredContractByHash(): StoredContractByHash | undefined
        ```
      
        * Returns the `StoredContractByHash` representation of the object, and `undefined` if the data cannot be casted to `StoredContractByHash`.
      
      * ```typescript
        public isStoredContractByName(): boolean
        ```
      
        * Returns `true` is the `ExecutableDeployItem` represents a `StoredContractByName`, and `false` otherwise.
      
      * ```typescript
        public asStoredContractByName(): StoredContractByName | undefined
        ```
      
        * Returns the `StoredContractByName` representation of the object, and `undefined` if the data cannot be casted to `StoredContractByName`.
      
      * ```typescript
        public isStoredVersionContractByName(): boolean
        ```
      
        * Returns `true` is the `ExecutableDeployItem` represents a `StoredVersionedContractByName`, and `false` otherwise.
      
      * ```typescript
        public asStoredVersionContractByName(): StoredVersionedContractByName | undefined
        ```
      
        * Returns the `StoredVersionedContractByName` representation of the object, and `undefined` if the data cannot be casted to `StoredVersionedContractByName`.
      
      * ```typescript
        public isStoredVersionContractByHash(): boolean
        ```
      
        * Returns `true` is the `ExecutableDeployItem` represents a `StoredVersionedContractByHash`, and `false` otherwise.
      
      * ```typescript
        public asStoredVersionContractByHash(): StoredVersionedContractByHash | undefined
        ```
      
        * Returns the `StoredVersionedContractByHash` representation of the object, and `undefined` if the data cannot be casted to `StoredVersionedContractByHash`.
      
      * ```typescript
        public isTransfer(): boolean
        ```
      
        * Returns `true` is the `ExecutableDeployItem` conforms to a `Transfer`, and `false` otherwise.
      
      * ```typescript
        public asTransfer(): boolean
        ```
      
        * Returns the `Transfer` representation of the object, and `undefined` if the data cannot be casted to `Transfer`.
    
  * #### `Deploy`
  
    The `Deploy` object contains a deploy complete with a linked or raw smart contract and the requesters' signature(s).
  
    * Objects
      * `public hash: Uint8Array`
        * The deploy hash of the `Deploy`
      * `public header: DeployHeader`
        * The header of the deploy.
      * `public payment: ExecutableDeployItem`
        * `ExecutableDeployItem` representation of payment logic, measured in motes.
      * `public session: ExecutableDeployItem`
        * Session logic of the deploy, typed as an `ExecutableDeployItem`
      * `public approvals: Approval[]`
        * Array of `Approval`s, referencing signatures and public keys of the signers who approve the deploy.
      
    * Methods
      
      * ```typescript
        constructor(
            hash: Uint8Array,
            header: DeployHeader,
            payment: ExecutableDeployItem,
            session: ExecutableDeployItem,
            approvals: Approval[]
        )
        ```
      
        * Constructs a `Deploy` object  from the deploy hash, header, payment logic, session code, and a single `Approval`, or in the case of multi-sig transactions, a set of `Approval`s.
      
      * ```typescript
        public isTransfer(): boolean
        ```
      
        * Returns `true` if the `Deploy` 's `session` represents a transfer, `false` otherwise.
      
      * ```typescript
        public isStandardPayment(): boolean
        ```
      
        * Returns `true` if the `Deploy`'s `payment` is a standard payment, `false` otherwise.
      
      * ```typescript
        public async send(nodeUrl: string): Promise<string>
        ```
      
        * Instantiates a `CasperClient` using its `nodeUrl` and executes `CasperClient(nodeUrl).putDeploy(this)` on the `Deploy`, returning a `Promise` which yields a deploy hash if successful, and rejects otherwise.
      
      * ```typescript
        public sign(keys: AsymmetricKey[]): Deploy
        ```
      
        * Signs and returns the otherwise identical `Deploy` using the signing `AsymmetricKey`, or for multi-signature transactions, an array of `AsymmetricKey`s. If using one key-pair, pass in an `AsymmetricKey` array of length 1.
