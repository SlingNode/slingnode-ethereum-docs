# Checkpoint sync

Consensus clients support syncing from a recent finalized checkpoint. This is substantially faster than syncing from genesis, while still providing all the same features.  For more details on this feature see [Lighthouse's documentation](https://lighthouse-book.sigmaprime.io/checkpoint-sync.html).&#x20;

slingnode.ethereum role enables checkpoint sync by default using one of the [community maintained endpoints](https://eth-clients.github.io/checkpoint-sync-endpoints/).&#x20;

## Variables

Checkpoint sync can be disabled by setting the consensus\_checkpoint\_sync\_enabled to false.&#x20;

The checkpoint endpoint defaults to Goerli (the same as the network). If you want to use checkpoint sync the on network other than Goerli, you will need to set the consensus\_checkpoint\_sync\_url variable to the desired endpoint (public or private).&#x20;

The variables are common for all clients, and are defined in 'defaults/main/main.yml'.&#x20;

```yaml
consensus_checkpoint_sync_enabled: true
consensus_checkpoint_sync_url: https://sync-goerli.beaconcha.in
```

## Client support

Technically, all clients support checkpoint sync however when using slingnode.ethereum with Nimbus this is feature is not currently available. This is due to the way Nimbus implements the checkpoint sync (it requires starting and restarting the client with different flags which is cumbersome to implement cleanly in Docker and Ansible). The table below shows support matrix and links to the client specific documentation for reference.

<table><thead><tr><th width="153.33333333333331">Client</th><th width="227" align="center">Available using the role</th><th>Official docs</th></tr></thead><tbody><tr><td>Lighthouse</td><td align="center">yes</td><td><a href="https://lighthouse-book.sigmaprime.io/checkpoint-sync.html">https://lighthouse-book.sigmaprime.io/checkpoint-sync.html</a></td></tr><tr><td>Prysm</td><td align="center">yes</td><td><a href="https://docs.prylabs.network/docs/prysm-usage/checkpoint-sync">https://docs.prylabs.network/docs/prysm-usage/checkpoint-sync</a></td></tr><tr><td>Teku</td><td align="center">yes</td><td><a href="https://docs.teku.consensys.net/HowTo/Get-Started/Checkpoint-Start/">https://docs.teku.consensys.net/HowTo/Get-Started/Checkpoint-Start/</a></td></tr><tr><td>Nimbus</td><td align="center">no</td><td><a href="https://nimbus.guide/trusted-node-sync.html">https://nimbus.guide/trusted-node-sync.html</a></td></tr></tbody></table>

## Public endpoint availability

It happens that the public endpoints are temporarily unavailable. If that is the case the client will fail to start. If the consensus client fails to start during the initial deployment, this may be the reason. To verify if that is the case, either check the logs in your log analytics solution or login to the server you are deploying to and check container logs.&#x20;

```sh
sudo docker logs --follow consensus
```

The exact error message depends on the client.&#x20;

### Lighthouse

```
{"msg":"Remote BN does not support EIP-4881 fast deposit sync","level":"WARN","ts":"2023-03-15T11:04:03.933663777Z","service":"beacon"}
{"msg":"Failed to start beacon node","level":"CRIT","ts":"2023-03-15T11:04:04.054012397Z","reason":"Finalized block missing from remote, it returned 404"}
```

### Prysm

<pre><code><strong>could not load genesis from file: Error retrieving genesis state from https://example.com/: error requesting state by id = genesis: code=404, url=https://example.com/eth/v2/debug/beacon/states/genesis, body=response body:
</strong></code></pre>

### Teku

```
2023-03-15 11:06:18.011 FATAL - Failed to load initial state from both https://example.com/ and https://example.com/eth/v2/debug/beacon/states/finalized : Invalid SSZ: trying to read more bytes than available
```
