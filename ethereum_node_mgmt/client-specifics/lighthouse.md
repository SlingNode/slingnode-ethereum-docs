# Lighthouse

## Overview

Lighthouse validator client's data dir structure before imports.

```
.
└── validators
    ├── slashing_protection.sqlite
    ├── slashing_protection.sqlite-journal
    ├── validator_definitions.yml
    └── validator_key_cache.json
```

## Import validator keys

During interactive import Lighthouse will prompt for keystore password. --password-file speciifes a text file containing the password Lighthouse will use to decrypt the keystores. It will import all keystores present in the import directory (--directory). All keystores must use the same password. Using  --reuse-password flag tell Lighthouse to save the password for future reuse otherwise the password would have to be specified interactively evertime the client restarts.  The password is saved in palin text on the validator server.

Relevant flags:

```yaml
- --directory={{ container_keystore_dir }}  # Import source directory
- --reuse-password     # Save the password 
- --password-file      # Path to the password file 
```

### Import command output

```
Running account manager for goerli network
validator-dir path: "/var/lib/lighthouse/validators"
WARNING: DO NOT USE THE ORIGINAL KEYSTORES TO VALIDATE WITH ANOTHER CLIENT, OR YOU WILL GET SLASHED.

Keystore found at "/keystore/keystore-m_12381_3600_1_0_0-1680087924.json":

 - Public key: 0x88a471158d618a8f9997dcb2cc1921411392d82d00e339ccf912fd9335bd42f97c9de046280d9d5f681a8e73a7d3baad
 - UUID: 57fdd35e-f227-40c3-8011-0b04468e9497

If you enter the password it will be stored as plain-text in validator_definitions.yml so that it is not required each time the validator client starts.

Enter the keystore password, or press enter to omit it:
Password is correct.

Successfully imported keystore.
Successfully updated validator_definitions.yml.

Keystore found at "/keystore/keystore-m_12381_3600_2_0_0-1680087924.json":

 - Public key: 0x99c4c42fac7d1393956bd9e2785ed67cf5aaca4bf56d2fcda94c42d6042aebb1723ce6bac6f0216ff8c5d4f9f013008b
 - UUID: 6d4913af-4cc2-457f-a962-39eca6d0dd37

If you enter the password it will be stored as plain-text in validator_definitions.yml so that it is not required each time the validator client starts.
Reuse previous password.
Successfully imported keystore.
Successfully updated validator_definitions.yml.

Successfully imported 2 validators (0 skipped).

WARNING: DO NOT USE THE ORIGINAL KEYSTORES TO VALIDATE WITH ANOTHER CLIENT, OR YOU WILL GET SLASHED.
```

### Data dir structure after keys import

```
.
└── validators
    ├── 0x88a471158d618a8f9997dcb2cc1921411392d82d00e339ccf912fd9335bd42f97c9de046280d9d5f681a8e73a7d3baad
    │   └── keystore-m_12381_3600_1_0_0-1680087924.json
    ├── 0x99c4c42fac7d1393956bd9e2785ed67cf5aaca4bf56d2fcda94c42d6042aebb1723ce6bac6f0216ff8c5d4f9f013008b
    │   └── keystore-m_12381_3600_2_0_0-1680087924.json
    ├── slashing_protection.sqlite
    ├── validator_definitions.yml
    └── validator_key_cache.json
```

## Slashing protection db import

### Import command output

```
Running account manager for goerli network
validator-dir path: "/var/lib/lighthouse/validators"
Loading JSON file into memory & deserializing [done].
All records imported successfully:
- 0x876a9a7fadb5b9d2114a5180f9fe50b451cbab5f241b42e476b724a3575e5a8277767bc5a7c831c63f066a9a725c53d6
    - latest proposed block: slot 4866645
    - latest attestation: epoch 154316 => epoch 154317
Import completed successfully.
Please double-check that the latest blocks and attestations above match your expectations.
```

## Slashing protection DB export

### Export command output

```
Running account manager for goerli network
validator-dir path: "/var/lib/lighthouse/validators"
Export completed successfully
```
