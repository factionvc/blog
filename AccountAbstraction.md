# Meta-Wallets, MPC, and Account Abstraction

The rhetoric and memes of “not your keys, not your crypto” has long been murmured in the crypto, where the platonic ideal is the self sovereign individual. As impractical as this may be for the everyperson, recent events has shown why this is necessary. The collapse of FTX, Celsius, and Voyager were not failures of crypto - they were failures of trust delegation. When we delegate trust we introduce risk which may not be quantifiable at the time. On the other hand, managing seed phrases and private keys with perfect opsec is not the way to mass adoption. So the question of how to participate in crypto, with some degree of sovereignty, and not get rekt, has been looming for a web3 epoch.

At Faction we’re excited about the developments happening in the programmatic wallet/account abstraction space, as this opens a wealth of opportunities for more people to get involved and creates a lot of room for innovation. But first;

### What is Account Abstraction?

Vitalik and frens laid out some good bedtime reading in [EIP-4337](https://github.com/ethereum/EIPs/blob/3fd65b1a782912bfc18cb975c62c55f733c7c96e/EIPS/eip-4337.md) // [this blog post](https://medium.com/infinitism/erc-4337-account-abstraction-without-ethereum-protocol-changes-d75c9d94dc4a). In essence, instead of your wallet having all the responsibilities of security, validation, and sending transactions, EIP-4337 introduces the idea of a “meta-wallet” - or, a wallet as code. Users still have their native wallets, but each interaction they perform on chain is a proto-smart contract, sequenced into a bundle (or rolled up, if you will). For example, instead of depositing liquidity into an LP, you would execute the `deposit` wallet action. This smart contract (or **********EntryPoint********** in the EIP parlance) can perform additional verification, security, and processing operations. At the most basic level, it’s easy to imagine a wallet nonce being delegated to this layer. But more involved use cases might involve different, less expensive forms of private key cryptography, social recovery, or even role based access control (RBAC).

Glossing over a lot of details, but there are three important points to call out

1. Wallet actions (fka transactions) can rolled up by the bundler and committed as an atomic transaction, effectively combining multiple actions as one transaction
2. These wallet actions are just like any other smart contract operation, in that all the interesting things happen in ********callData. callData******** is less interpretable than other forms of transaction data, and typically requires a “decoder” - ie, an ABI.
3. Because wallet actions can be stacked, each of them must be idempotent - ie, how they are executed must be similar to how they are simulated - so can’t touch the global memory state of Ethereum.

Introducing this additional layer of complexity has some important implications, specifically when it comes to user experience. Let’s explore them.

### Implications of Account Abstraction

- Additional verification and security logic could be introduced via the [diamond-proxy pattern](https://eips.ethereum.org/EIPS/eip-2535), creating an open source marketplace of wallet plugins. Presumably the ones that gain adoption will be open source and gather collective scrutiny/contributions, so it’s possible this could streamline protocol-level public goods development.
- However the additional complexity of needing to create “wallet action transactions” and then roll them up, even in bundles of one transaction, will be marginally more expensive, marginally slower to confirm, and slightly more complex to work with. On the spectrum of ease of use vs security, there will always be a use case for simplicity.
- Having the option to host RBAC and other logic in an *******EntryPoint******* provides a good answer to one of the core challenges to EVM-based chains. Today, roles and permissions are either governed by modifiers (ie, `onlyOwner`) or multisigs, but these each have limitations in terms of customizability, granularity, and team size.
- . This modular design allows for additional encryption schemas, social recovery, and so on. Some examples of what this might unlock are
    - Quantum safety on the application layer (as per Vitalik’s paper)
    - Social recovery for every transaction, so auditable cold storage access
    - More expressive identity and access management in DAOs, ie, applying the principle of least privilege to actors, greatly improving security
- This form of access control could still (and does) happen via offchain Multi-Party Compute (MPC), but this is cumbersome once there’s sufficient complexity, not auditable, and not composable with modular logic.

### Is there still room for MPC?

Even though account abstraction creates more room for security, a wallet’s initial private key will always need to be safeguarded. Social recovery for every transaction is possible, but it gets expensive quickly. So presumably there will still be a need for secure off-chain key storage and recovery. Not to mention EIP-4337 only applies to Ethereum and other EVM chains that choose to adopt it.

Due to its limitations, MPC has been (somewhat unfairly) been ostracized to the domain of key management - but there’s a lot more that can be done with the technology. We might expand upon this in a later article, but generally, we’re fans of the web3 principles of customizability, modularity, and extensibility, as that introduces so much more surface area for innovation. Despite the core capabilities being an open standard, there’s a lot of interesting opportunities here!

If you’re building in this domain, please let us know!