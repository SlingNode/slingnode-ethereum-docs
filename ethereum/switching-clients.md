# Switching clients

You may want to switch clients if you are having issues with the one you are running or if it becomes the majority client.  To switch the clients using slingnode.ethereum role you only need to update the ["clients" variable](role-variables.md#clients) and re-run the role.&#x20;

* switching clients using the described method will incur downtime while your clients re-sync, refer to the [considerations](switching-clients.md#switching-considerations) section for details
* the role does not delete previous client's data directories
* the role does not migrate validator keys

If you are running Geth and Lighthouse your "clients" variable looks as follows:&#x20;

```yaml
clients:
  execution: geth
  consensus: lighthouse
  validator: lighthouse
```

You want to switch to Nethermind and Prysm you'd  update your vars to as shown below and re-run the role.&#x20;

```yaml
clients:
  execution: nethermind
  consensus: prysm
  validator: prysm
```

You can switch all or any of the layers using this method.&#x20;

## Switching considerations&#x20;

This section  describes what to expect when switching specific layers.

### Execution

The execution client will need to sync. The time it takes depends on many variables but in any case this will take days during which your node will not be able to perform Validator duties (if you run a Validator).&#x20;

#### Switching back to the previous execution client

If you are switching back to a client that you used before and you did not delete the data directory, the client will pick up the existing database and sync from there. For example if you are testing clients you could sync all four of them and then switch among them with a minimal downtime (depending on how long the client was offline). Make sure there is a sufficient disk space.&#x20;

### Consensus

The consensus client will need to sync. Consensus clients support checkpoint sync which lets them be up and running in minutes, therefore the downtime incurred should be minimal. Checkpoint sync is enabled by default when deploying clients using slingnode.ethereum role, refer to [Checkpoint sync](checkpoint-sync.md) page for details.&#x20;

#### Switching back to the previous consensus client

In most cases it is desirable to delete the old data directory of a consensus client before switching back to it. This is because if a client finds an existing data directory it will disable the checkpoint sync and will start normal sync for the point where it left off. In most cases this will take longer to complete than the fresh sync from the checkpoint.&#x20;

### Validator

Validators don't have any state to sync, but they have keys and slashing protection DB to consider. Migrating validator keys and Slashing protection DB is outside of the scope of slingnode.ethereum role. Please refer to [slingnode.ethereum\_node\_mgmt](https://slingnode.gitbook.io/slingnode.ethereum\_node\_mgmt/) role for those tasks.&#x20;

If you change the validator client the previous client will be stopped, new one deployed and started but the keys and slashing protection DB will not be migrated.&#x20;

## Data directories

The role does not delete the existing data directories. This lets you keep the old data directory should you choose to switch back (assuming sufficient disk space). If you do not want to keep the old data directories you will need to delete them for example using [slingnode.ethereum\_node\_mgmt](https://slingnode.gitbook.io/slingnode.ethereum\_node\_mgmt/) role.&#x20;

### Directory structure before and after switching

Following the example from the previous section, if you run Geth and Lighthouse your directory structure will look as follows:

```
blockchain/
├── blockchain_dc
│   ├── dc-blockchain-eth-consensus-lighthouse.yml
│   ├── dc-blockchain-eth-execution-geth.yml
│   └── dc-blockchain-eth-validator-lighthouse.yml
├── consensus
│   └── lighthouse
├── execution
│   └── geth
├── jwt
│   └── jwt.hex
├── scripts
│   └── prune_execution_client.py
└── validator
    └── lighthouse
```

After updating the clients var to Nethermind and Prysm and re-running the role, the directory structure will be:

```
blockchain/
├── blockchain_dc
│   ├── dc-blockchain-eth-consensus-prysm.yml
│   ├── dc-blockchain-eth-execution-nethermind.yml
│   └── dc-blockchain-eth-validator-prysm.yml
├── consensus
│   ├── lighthouse
│   └── prysm
├── execution
│   ├── geth
│   └── nethermind
├── jwt
│   └── jwt.hex
├── scripts
│   └── prune_execution_client.py
└── validator
    ├── lighthouse
    └── prysm
```
