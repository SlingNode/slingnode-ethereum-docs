# Client specific behavior

All container based tasks related to a specific client are defined in a single Docker Compose file. Each Docker Compose service corresponds to task. slingnode.ethereum\_node\_mgmt  role utilizes [Docker Compose profiles ](https://docs.docker.com/compose/profiles/)to selectively start containers. The role generates the compose files based on the Jinja template and copies them to the server.  The compose files use the following name convention:

```
dc-blockchain-eth-validator-client_name-mgmt.yml
```

Client specific subpages show the expected command line output for each successfully executed task.

## Troubleshooting

For troubleshooting purposes it may be desired to launch the compose services interactively at the server. To do so, you can run the Ansible role which will generate and copy the Compose file to the managed server. The role will attempt to start the compose service and leave the file in location defined in "blockchain\_docker\_compose\_path" variable which defaults to:

```yaml
blockchain_root_path: /opt/blockchain
blockchain_docker_compose_path: "{{ blockchain_root_path }}/blockchain_dc"
```

To execute the desired task interactively:

```sh
cd /opt/blockchain/blockchain_dc/
```

and run one of the below commands with the desired client name and profile.

```sh
docker compose -f dc-blockchain-eth-validator-prysm-mgmt.yml --profile import-validator-keys up
docker compose -f dc-blockchain-eth-validator-prysm-mgmt.yml --profile import-slashing-protection-db up
docker compose -f dc-blockchain-eth-validator-prysm-mgmt.yml --profile export-slashing-protection-db up
```
