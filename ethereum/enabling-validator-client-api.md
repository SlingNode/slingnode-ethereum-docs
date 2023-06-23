# Enabling Validator client API

slingnode.ethereum role disables validator client API by default. They can be easily enabled using variables described on this page.&#x20;

All clients except Teku serve the API over plain text HTTP. Teku is the only client that requires HTTPS for API access. The role generates a self-signed certificate, refer to [Teku validator client API access](enabling-validator-client-api.md#teku-validator-client-api-access) for details.

The API requests are authenticated using a bearer token. All clients except Nimbus auto-generate the token and save it in a client specific location within their data directory - refer to [Bearer token](enabling-validator-client-api.md#bearer-token) section for details.&#x20;

The table below shows summary of the relevant client features:&#x20;

<table><thead><tr><th width="147">Client</th><th width="150" align="center">Protocol</th><th width="225" align="center">Auto-generated token</th><th align="center">ETH2 Key manager API</th></tr></thead><tbody><tr><td>Lighthouse</td><td align="center">HTTP</td><td align="center">yes</td><td align="center">yes</td></tr><tr><td>Prysm</td><td align="center">HTTP</td><td align="center">yes</td><td align="center">yes</td></tr><tr><td>Teku</td><td align="center">HTTPS</td><td align="center">yes</td><td align="center">yes</td></tr><tr><td>Nimbus</td><td align="center">HTTP</td><td align="center">no</td><td align="center">yes</td></tr><tr><td>Lodestar</td><td align="center">HTTP</td><td align="center">yes</td><td align="center">yes</td></tr></tbody></table>

Since the APIs are served over plain text HTTP protocol (except Teku) it is recommended to use either a reverse proxy (such as Nginx) with TLS or SSH tunneling to enable access from remote hosts. Refer to [Exposing the validator API port to host](enabling-validator-client-api.md#exposing-the-validator-api-port-to-host) for details.&#x20;

## Enabling API

To enable the validator API set the following variable to true:

```yaml
validator_api_enabled: true
```

This will enable the validator API and expose the default port (TCP 7500) to the Docker network - meaning the API will be accessible only from other containers connected to the same network.&#x20;

Note Lodestar is an exception and uses a different variable, refer to Lodestar section below for details.&#x20;

### Exposing the validator API port to host

Refer to [Exposing ports](architecture/exposing-ports.md) page for general overview of how this is implemented in the role.

In order to make the API accessible from the Docker host, set the following variable to true:

```yaml
expose_validator_api_port_to_host: true
```

This will expose the port to the host and map it to the host's loop-back interface (127.0.0.1) as shown below:

```
NAMES           PORTS
validator	127.0.0.1:7500->7500/tcp
```

With this configuration you can install and configure either a reverse proxy or SSH tunnel to enable secure remote access to the API.&#x20;

### Exposing the validator API port to remote hosts

<mark style="color:red;">**Configuration outlined in this section is suitable only if you run your servers on a private network behind a network firewall (or security groups), otherwise sensitive APIs will be exposed to the Internet even if your server has  FirewallD or UFW enabled due to how Docker modifies Iptables. See**</mark> [<mark style="color:red;">**Docker & host firewall**</mark>](architecture/docker-and-host-firewall.md) <mark style="color:red;">**for details.**</mark>&#x20;

Refer to [Exposing ports](architecture/exposing-ports.md) page for general overview of how this is implemented in the role.

To expose the port to remote hosts and make them accessible you can override the following variable and set it to:&#x20;

```yaml
host_ip_address_to_bind_to: 0.0.0.0
```

This will map the container port to the host's "all interfaces" IP address, as shown below:&#x20;

```
NAMES           PORTS
validator	0.0.0.0:7500->7500/tcp
```

### Changing API port

The default API port (7500) can be changed by overriding the following variable:

```yaml
validator_api_port: 7500
```

## Bearer token

All clients except Nimbus auto-generate the token and save it in a client specific location. The table below summarizes the Docker host location of the token for each client:

<table><thead><tr><th width="151">Client</th><th>Token location on Docker host</th></tr></thead><tbody><tr><td>Lighthouse</td><td>{{ blockchain_root_path }}/validator/lighthouse/validators/api-token.txt</td></tr><tr><td>Prysm</td><td>{{ blockchain_root_path }}/validator/prysm/auth-token</td></tr><tr><td>Teku</td><td>{{ blockchain_root_path }}/validator/teku/validator/key-manager/validator-api-bearer</td></tr><tr><td>Nimbus</td><td>{{ blockchain_root_path }}/validator/nimbus/api-token.txt</td></tr><tr><td>Lodestar</td><td>{{ blockchain_root_path }}/validator/lodestar/validator-db/api-token.txt</td></tr></tbody></table>

### Nimbus bearer token

All clients except Nimbus generate the bearer token automatically, refer to the client documentation for details. slingnode.ethereum will generate the token (if it doesn't exist) for Nimbus and will save it in the following location on the Docker host:

```yaml
nimbus_host_keymanager_token_file: "{{ blockchain_root_path }}/validator/nimbus/api-token.txt"
```

#### Regenerating the token

If the token already exist the generation task is skipped. If you want to re-generate the token, you can override the following variables and set them to true:&#x20;

```yaml
nimbus_recreate_keymanager_token: true
nimbus_restart_validator_container: true
```

This tells the role to create new a token and restart the validator container.&#x20;

NOTE: you need to remove the above variables from your playbook when the chagne has been completed, otherwise the token will be generated and the container restarted every time the role runs.&#x20;

## Teku validator client API access

Teku enforces HTTPS for the API. It [requires a certificate](https://docs.teku.consensys.net/HowTo/External-Signer/Manage-keys#enable-validator-client-api) with a valid Subject or Subject Alternative Name (SAN) to enable access - otherwise a SNI error will be returned.

slingnode.ethereum generates the certificate automatically when it deploys Teku validator. By default the following DNS names are in included in the SAN extension (in addition to 127.0.0.1 in the subject).

```yaml
teku_validator_api_certificate_subject_alternative_names: DNS:validator,DNS:localhost
```

The default configuration will allow access from the Docker host and Docker network. If you want to allow access using IP address or a fully qualified domain name, you can add additional alternative names as shown below (validator and localhost can be remove if they aren't needed):

```yaml
teku_validator_api_certificate_subject_alternative_names: DNS:sn-eu-val01.slingnode.com,IP:192.168.121.66
```

NOTE: This is a self-signed certificate which will not be trusted by browsers and other tools (such as curl).&#x20;

If you try to access the API using a hostname or IP that is not included in the certificate you will receive the following error:

```
HTTP ERROR 400 Invalid SNI
```

### Regenerating the certificate

If the certificate already exist the generation task is skipped. If you want to re-generate the certificate, you can override the following variables and set them to true:&#x20;

```yaml
teku_recreate_validator_api_keystore: true
teku_restart_validator_container: true
```

This tells the role to create new a certificate and restart the validator container.&#x20;

NOTE: you need to remove the above variables from your playbook otherwise the certificate will be generated and the container restarted every time the role runs.&#x20;

## Lodestar

Validator APIs are disabled by default for all clients except Lodestar. Lodestar cannot be started with the Validator API (key manager APIs) disabled when there's no existing keystore - meaning that in order to start it with the APIs disabled the keystore would have to be imported using CLI beforehand. To workaround this the role starts Lodestar Validator with the APIs enabled. Lodestar uses a different variable than the other clients to control if the APIs are enabled.&#x20;

```yaml
lodestar_validator_api_enabled: true
```

## Client documentation

For client specific implementation details refer to the official documentation.

{% embed url="https://lighthouse-book.sigmaprime.io/api-vc.html" %}
Lighthouse
{% endembed %}

{% embed url="https://docs.prylabs.network/docs/how-prysm-works/keymanager-api" %}
Prysm
{% endembed %}

{% embed url="https://docs.teku.consensys.net/Reference/Rest_API/Rest#enable-the-validator-client-api" %}
Teku
{% endembed %}

{% embed url="https://nimbus.guide/keymanager-api.html" %}
Nimbus
{% endembed %}
