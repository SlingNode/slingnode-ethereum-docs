# Teku

Teku validator client's data dir structure before imports.

```
.
├── validator
│   └── key-manager
│       ├── local
│       ├── local-passwords
│       └── validator-api-bearer
├── validator_api_key.crt
├── validator_api_key.key
├── validator_api_key.keystore
└── validator_api_key.password
```

## Import validator keys

Teku doesn't have import command. The client will read and import keystores from directory specified using --validator-keys flag passed to the Validator client. By default Nimbus deployed using slingnode.ethereum will point to the same directory for keys and the keystore password.&#x20;

```
- --validator-keys={{ teku_validator_keys }}
```

The role copies the keystores and passwords to the keystore directory on the server and restarts the container. Teku imports the keystores when it starts.

_From Teku docs:_

_Directory or file to load the encrypted keystore file(s) and associated password file(s) from. Keystore files must use the `.json` file extension, and password files must use the `.txt` file extension. When specifying directories, Teku expects to find identically named keystore and password files. For example `validator_217179e.json` and `validator_217179e.txt`._

{% embed url="https://docs.teku.consensys.net/Reference/CLI/CLI-Syntax#validator-keys" %}
Managing validator keys
{% endembed %}

### Data dir structure after keys import

There's no changes to the file layout.&#x20;

```
.
├── validator
│   └── key-manager
│       ├── local
│       ├── local-passwords
│       └── validator-api-bearer
├── validator_api_key.crt
├── validator_api_key.key
├── validator_api_key.keystore
└── validator_api_key.password
```

## Slashing protection db import

### Import command output

```
Reading slashing protection data from: /slashing_protection_db/slashing_protection.json
Writing slashing protection data to: /var/lib/teku/validator/slashprotection
Importing 876a9a7fadb5b9d2114a5180f9fe50b451cbab5f241b42e476b724a3575e5a8277767bc5a7c831c63f066a9a725c53d6
Updated 1 validator slashing protection records
```

## Slashing protection DB export

### Export command output

```
Reading slashing protection data from: /var/lib/teku/validator/slashprotection
Exporting 876a9a7fadb5b9d2114a5180f9fe50b451cbab5f241b42e476b724a3575e5a8277767bc5a7c831c63f066a9a725c53d6
Writing slashing protection data to: /slashing_protection_db/slashing_protection.json
Wrote 1 validator slashing protection records to /slashing_protection_db/slashing_protection.json
```
