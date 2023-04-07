# Prysm

Prysm validator client's data dir structure before imports.

```
.
├── auth-token
├── tosaccepted
└── validator.db
```

## Import validator keys

Command based key import will create Prysm wallet with password specified in the --wallet-password-file flag. The role will copy the wallet password file specifeid using prysm\_wallet\_password variable from the control host to the server.&#x20;

```yaml
prysm_wallet_password:
    - src: molecule/resources/test_validator_keystores/{{ prysm_wallet_file_name }}
      dest: "{{ blockchain_root_path }}/validator/prysm/{{ prysm_wallet_file_name }}"
```

All keystores located in the import dir (--keys-dir) will be imported. They all must use the same password (--account-password-file).&#x20;

Relevant flags:

```django
- --keys-dir={{ container_keystore_dir }}
- --wallet-dir={{ prysm_wallet_dir }}
- --wallet-password-file={{ prysm_wallet_password_file }}
- --account-password-file={{ container_keystore_dir }}/{{ keystore_password_file }}
```

### Import command output

```
{"level":"warning","msg":"Running on the Prater Testnet","prefix":"flags","time":"2023-04-03T08:18:24Z"}
{"level":"info","msg":"Successfully created new wallet","prefix":"accounts","time":"2023-04-03T08:18:24Z","wallet-path":"/var/lib/prysm"}
{"level":"warning","msg":"You are using an insecure gRPC connection. If you are running your beacon node and validator on the same machines, you can ignore this message. If you want to know how to enable secure connections, see: https://docs.prylabs.network/docs/prysm-usage/secure-grpc","prefix":"validator","time":"2023-04-03T08:18:24Z"}
Importing accounts, this may take a while...
Importing accounts... 100% [==========================================]  [0s:0s]
Successfully imported 2 accounts, view all of them by running `accounts list`
```

### Data dir structure after keys import

```
.
├── auth-token
├── direct
│   └── accounts
│       └── all-accounts.keystore.json
├── prysm_wallet_pass.txt
├── tosaccepted
└── validator.db
```

## Slashing protection db import

### Import command output

```
{"level":"warning","msg":"Running on the Prater Testnet","prefix":"flags","time":"2023-04-03T08:30:49Z"}
{"level":"info","msg":"Found existing validator.db inside of /var/lib/prysm","prefix":"historycmd","time":"2023-04-03T08:30:49Z"}
{"level":"info","msg":"Starting import of slashing protection file /slashing_protection_db/slashing_protection.json","prefix":"historycmd","time":"2023-04-03T08:30:49Z"}
Importing proposals for validator public key 0x876a9a7fadb5 100% [====]  [0s:0s]
Importing attesting history for validator public keys 100% [==========]  [0s:0s]
{"level":"info","msg":"Slashing protection JSON successfully imported into /var/lib/prysm","prefix":"historycmd","time":"2023-04-03T08:30:49Z"}
```

## Slashing protection DB export

### Export command output

```
{"level":"info","msg":"Writing slashing protection export JSON file to /slashing_protection_db/slashing_protection.json","prefix":"historycmd","time":"2023-03-30T08:57:44Z"}
{"level":"info","msg":"Successfully wrote /slashing_protection_db/slashing_protection.json. You can import this file using Prysm's validator slashing-protection-history import command in another machine","prefix":"historycmd","time":"2023-03-30T08:57:44Z"}
```

