# Witval Chain Information

Contents

* [Status](#status)
* [Chain Data](#chain-data)

## Status

* Timeline
  * 2022-11-28: Spawn time: `2022-11-29T14:00:00.000000Z`
  * 2022-11-26: Proposal 14 voting period ends
  * 2022-11-25: Proposal 14 goes into voting period
  * 2022-11-25: Genesis file without CCV state is generated

witval will launch as a consumer chain through a governance proposal in the `provider` chain. Read the [Consumer Chain Start Process](/docs/Consumer-Chain-Start-Process.md) page for more details about the workflow.

The following items will be included in the proposal:
* Genesis file hash
  * The SHA256 is used to verify against the genesis file that the proposer has made available for review.
  * This "fresh" genesis file cannot be used to run the chain: it must be updated with the CCV states after the spawn time is reached.
* Binary hash
* Spawn time
  * Even if the proposal passes, the CCV states will not be available from the provider chain until after the spawn time is reached.

## Chain Data

### Binary

The binary published in this repo is the `witvalchain` binary built using the `vitwit/witvalchain` repo tag [v0.1.0](https://github.com/vitwit/witvalchain/releases/tag/v0.1.0). You can generate the binary following the [Get Started section](https://github.com/vitwit/witvalchain/tree/v0.1.0#get-started).


### Verify Binary Checksum.
Binary checksums can differ based on many things to include go, libc, and make versions. To get a consistent checksum you can use something like docker to verify.

  * [Linux amd64 build](witvalchain)
  * Version: `v0.1.0`
  * SHA256: `61adf7eee62919db3f64cf1808f0ad4995322c0f70f7ed8ad594ad4b23ae4c64`

### Genesis file

**The genesis file for Interchain Security consumer chains must include the CCV (Cross Chain Validation) state generated by the provider chain _after_ the spawn time is reached.**

Final genesis file **with CCV state**: **`pending spawn time`**
- SHA256: `pending spawn time`
- Validators must replace their `config/genesis.json` file with this one before running the binary.

The genesis file with was generated using the following settings:

* Chain ID: `witvalchain`
* Denom: `uwit`
* Signed blocks window: `"8640"`
* Genesis accounts were added to provide funds for a faucet and a relayer that will be run by the testnet coordinators.
* Genesis file **without CCV state**: [`witvalchain-fresh-genesis.json`](witvalchain-fresh-genesis.json), SHA256: `ba4a57b8f3e958a8c108d7f3e2180deb520dacea1d98880a61ce550490e353b1`
  * **This is provided only for verification, this is not the genesis file validators should be running their nodes with.**

## Endpoints

* **p2p seeds : `Pending`**
* **p2p persistent peers : `Pending`**

* Please keep in mind that any validator that does not come online after 67% of the voting power is up and running, is likely to be slashed for downtime, potentially resulting in being jailed (the `signed_blocks_window` parameter is set to `8640`).

## Join via Bash Script

On the node machine:
- Copy the `node_key.json` and `priv_validator_key.json` files for your validator.
  - **These should be the same ones as the ones from your provider node**.
- Run one of the following scripts:
  - witvalchain service: [witvalchain-init.sh](witvalchain-init.sh)
  - Cosmovisor service: [witvalchain-init-cv.sh](witvalchain-init-cv.sh)
- Wait until the spawn time is reached and the genesis file with the CCV states is available.
- Overwrite the genesis file with the one that includes the CCV states.
  - The default location is `$HOME/.witvalchain/config/genesis.json`.
- Enable and start the service:
  - witvalchain
    ```
    sudo systemctl enable witvalchain
    sudo systemctl start witvalchain
    ```
  - Cosmovisor
    ```
    sudo systemctl enable cv-witvalchain
    sudo systemctl start cv-witvalchain
    ```
- To follow the log, use:
  - witvalchain: `journalctl -fu witvalchain`
  - Cosmovisor: `journalctl -fu cv-witvalchain`
- If the log does not show up right away, run `systemctl restart systemd-journald`.