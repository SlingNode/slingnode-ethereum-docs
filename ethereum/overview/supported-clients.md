# Supported clients

Supported execution clients:

* Geth
* Nethermind
* Besu
* Erigon

Supported consensus clients:&#x20;

* Lighthouse
* Prysm
* Teku
* Nimbus (checkpoint sync currently not supported)

Supported validator clients:&#x20;

* Lighthouse
* Prysm
* Teku
* Nimbus\*

### Managing validators

slingnode.ethereum enables deployment of the validator clients but doesn't include feature to perform validator management tasks such as:

* importing keys
* exporting/importing slashing protection database

Those can be accomplished using a [separate role - slingnode.ethereum\_node\_mgmt. ](https://slingnode.gitbook.io/slingnode.ethereum\_node\_mgmt/)

Alternatively, the [Ethereum keymanager API](https://ethereum.github.io/keymanager-APIs/) can be used to handle those tasks. Validator API are disabled by default, refer to [Enabling Validator Client API](../enabling-validator-client-api.md) for the guide on how to deploy the validator clients with the API enabled and accessible.

#### Why two separate roles?&#x20;

The roles were separated in order to preserve separation of concerns. The roles can be thought of as follows:&#x20;

* slingnode.ethereum is a configuration and deployment role
* slingnode.ethereum\_node\_mgmt is a management role&#x20;

Combining both together would make them overly complicated and more difficult to understand. We like keeping it simple.&#x20;
