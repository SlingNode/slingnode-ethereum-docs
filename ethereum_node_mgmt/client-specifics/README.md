# Client specifics

slingnode.ethereum  role utilizes [Docker Compose profiles ](https://docs.docker.com/compose/profiles/)to selectively start containers. There are three profiles. The role generates the compose files based on the Jinja template and copies them to the server.  For troubleshooting purposes they can be launched interactively at the server using the following commands:

```sh
docker compose -f dc-blockchain-eth-validator-prysm-mgmt.yml --profile import-validator-keys
docker compose -f dc-blockchain-eth-validator-prysm-mgmt.yml --profile import-slashing-protection-db up
docker compose -f dc-blockchain-eth-validator-prysm-mgmt.yml --profile export-slashing-protection-db up
```

For client specific details refer to the subpages. &#x20;
