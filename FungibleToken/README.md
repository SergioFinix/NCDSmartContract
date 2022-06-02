Ejemplo de Token Fungible (FT)
===================

Este ejemplo es un Fork de: https://github.com/near-examples/FT

Example implementation of a [Fungible Token] contract which uses [near-contract-standards] and [simulation] tests. This is a contract-only example.

  [Fungible Token]: https://nomicon.io/Standards/FungibleToken/Core
  [near-contract-standards]: https://github.com/near/near-sdk-rs/tree/master/near-contract-standards
  [simulation]: https://github.com/near/near-sdk-rs/tree/master/near-sdk-sim

Prerequisites
=============

If you're using Gitpod, you can skip this step.

1. Make sure Rust is installed per the prerequisites in [`near-sdk-rs`](https://github.com/near/near-sdk-rs#pre-requisites)
2. Ensure `near-cli` is installed by running `near --version`. If not installed, install with: `npm install -g near-cli`

## Building

To build run:
```bash
cd scripts && ./build.sh
```

Usando este Contrato
===================

This smart contract will get deployed to your NEAR account. For this example, please create a new NEAR account. Because NEAR allows the ability to upgrade contracts on the same account, initialization functions must be cleared. If you'd like to run this example on a NEAR account that has had prior contracts deployed, please use the `near-cli` command `near delete`, and then recreate it in Wallet. To create (or recreate) an account, please follow the directions on [NEAR Wallet](https://wallet.near.org/).

In the project root, log in to your newly created account  with `near-cli` by following the instructions after this command:

    near login

To make this tutorial easier to copy/paste, we're going to set an environment variable for your account id. In the below command, replace `MY_ACCOUNT_NAME` with the account name you just logged in with, including the `.near`:

    ID=refugiolomito.testnet

You can tell if the environment variable is set correctly if your command line prints the account name after this command:

    echo $ID

Now we can deploy the compiled contract in this example to your account:

    near deploy --wasmFile res/fungible_token.wasm --accountId $ID

FT contract should be initialized before usage. You can read more about metadata at ['nomicon.io'](https://nomicon.io/Standards/FungibleToken/Metadata.html#reference-level-explanation). Modify the parameters and create a token:

    near call $ID new '{"owner_id": "'$ID'", "total_supply": "1000000", "metadata": { "spec": "ft-1.0.0", "name": "Perrito Token", "symbol": "PRTO", "decimals": 8 }}' --accountId $ID

Get metadata:

    near view $ID ft_metadata


Ejemplo de Transferencia de Token
---------------

Usaremos la cuenta do0k13.testnet para trasferir fondos.

Se agrega la variable storage_deposit para la cuenta:

    near call $ID storage_deposit '{"account_id": "do0k13.testnet", "registration_only": true }' --accountId do0k13.testnet --amount 0.00125


Se valida el balance de la cuenta, actualmente debería ser cero:

    near view $ID ft_balance_of '{"account_id": "'do0k13.testnet'"}'

Se transfieren tokens a la wallet desde el contrato original, se debe adjuntar exactamente 1 yoctoNEAR de depósito:

    near call $ID ft_transfer '{"receiver_id": "'do0k13.testnet'", "amount": "19"}' --accountId $ID --amount 0.000000000000000000000001


Se valida nuevamente el balance y debería ser `19`.

## Testing

As with many Rust libraries and contracts, there are tests in the main fungible token implementation at `ft/src/lib.rs`.

Additionally, this project has [simulation] tests in `tests/sim`. Simulation tests allow testing cross-contract calls, which is crucial to ensuring that the `ft_transfer_call` function works properly. These simulation tests are the reason this project has the file structure it does. Note that the root project has a `Cargo.toml` which sets it up as a workspace. `ft` and `test-contract-defi` are both small & focused contract projects, the latter only existing for simulation tests. The root project imports `near-sdk-sim` and tests interaction between these contracts.

You can run unit tests with the following command:

```bash
cd ft && cargo test -- --nocapture --color=always
```

You can run integration tests with the following commands:
*Rust*
```bash
cd integration-tests/rs && cargo run --example integration-tests
```
*TypeScript*
```bash
cd integration-tests/ts && yarn && yarn test
```

## Notes

 - The maximum balance value is limited by U128 (`2**128 - 1`).
 - JSON calls should pass U128 as a base-10 string. E.g. "100".
 - This does not include escrow functionality, as `ft_transfer_call` provides a superior approach. An escrow system can, of course, be added as a separate contract or additional functionality within this contract.

## No AssemblyScript?

[near-contract-standards] is currently Rust-only. We strongly suggest using this library to create your own Fungible Token contract to ensure it works as expected.

Someday NEAR core or community contributors may provide a similar library for AssemblyScript, at which point this example will be updated to include both a Rust and AssemblyScript version.

## Contributing

When making changes to the files in `ft` or `test-contract-defi`, remember to use `./build.sh` to compile all contracts and copy the output to the `res` folder. If you forget this, **the simulation tests will not use the latest versions**.

Note that if the `rust-toolchain` file in this repository changes, please make sure to update the `.gitpod.Dockerfile` to explicitly specify using that as default as well.
