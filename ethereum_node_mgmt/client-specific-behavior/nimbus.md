# Nimbus

Nimbus validator client's data directory structure before imports.

```
.
└── validators
```

## Import validator keys

Automated command line import is not supported by the role for Nimbus. The client requires the keystore password to be entered interactively during the import which is does not work well with ephemeral containers. For Nimbus only API based key import is supported.&#x20;

### Interactive import

It is possible to import the keys by accessing the server directly and starting the container with the following volumes.&#x20;

```sh
docker run -it --rm --entrypoint /bin/bash -v /opt/blockchain/validator/nimbus:/var/lib/nimbus:rw -v /opt/blockchain/validator/keystore:/keystore:ro -u 39284:39284 statusim/nimbus-eth2:amd64-v23.3.2 
```

### Import command output

```
I have no name!@a5b42bce959c:/home/user$ ./nimbus_beacon_node --data-dir=/var/lib/nimbus deposits import /keystore
Please enter the password for decrypting '/keystore/keystore-m_12381_3600_1_0_0-1680087924.json' or press ENTER to skip importing this keystore
Password: 
NOT 2023-04-03 06:40:09.315+00:00 Keystore imported                          file=/keystore/keystore-m_12381_3600_1_0_0-1680087924.json
NOT 2023-04-03 06:40:10.601+00:00 Keystore imported                          file=/keystore/keystore-m_12381_3600_2_0_0-1680087924.json
```

### Data dir structure after keys import

```
.
├── secrets
│   ├── 0x88a471158d618a8f9997dcb2cc1921411392d82d00e339ccf912fd9335bd42f97c9de046280d9d5f681a8e73a7d3baad
│   └── 0x99c4c42fac7d1393956bd9e2785ed67cf5aaca4bf56d2fcda94c42d6042aebb1723ce6bac6f0216ff8c5d4f9f013008b
└── validators
    ├── 0x88a471158d618a8f9997dcb2cc1921411392d82d00e339ccf912fd9335bd42f97c9de046280d9d5f681a8e73a7d3baad
    │   └── keystore.json
    └── 0x99c4c42fac7d1393956bd9e2785ed67cf5aaca4bf56d2fcda94c42d6042aebb1723ce6bac6f0216ff8c5d4f9f013008b
        └── keystore.json
```

## Slashing protection db import

### Import command output

```
INF 2023-04-01 07:27:51.055+00:00 No slashing protection data for validator - initiating topics="antislash" validator=876a9a7fadb5b9d2114a5180f9fe50b451cbab5f241b42e476b724a3575e5a8277767bc5a7c831c63f066a9a725c53d6
Import finished: '/slashing_protection_db/slashing_protection.json' into '/var/lib/nimbus/validators/slashing_protection.sqlite3'
```

```
.
└── validators
    ├── slashing_protection.sqlite3
    ├── slashing_protection.sqlite3-shm
    └── slashing_protection.sqlite3-wal
```

## Slashing protection DB export

### Export command output

```
Exported slashing protection DB to '/slashing_protection_db/slashing_protection.json'
Export finished: '/var/lib/nimbus/validators/slashing_protection.sqlite3' into '/slashing_protection_db/slashing_protection.json'
```
