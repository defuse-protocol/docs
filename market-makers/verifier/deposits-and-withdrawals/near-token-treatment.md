# Near Token treatment

Given that the Near is the native token for the Near blockchain, implying the requirement to have special treatment for it, and in order to streamline the usage of Near with other assets, Near is treated as a fungible token ([NEP-141](https://nomicon.io/Standards/Tokens/FungibleToken/Core)) using its wrapped fungible token, [wrap.near](https://nearblocks.io/address/wrap.near). The source code (and interface) for wrapped Near can be found [here](https://github.com/near/core-contracts/).

Hence, there is no way to deposit Near directly. The user should exchange their Near token and exchange it for wrapped Near token in the wrap.near contract before depositing them into the Verifier contract.
