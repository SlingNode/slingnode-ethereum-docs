# JWT secret

Consensus clients connect to execution clients using  JSON-RPC over HTTP protocol. The HTTP requests between the clients are authenticated using a JWT (Java Web Token). To generate and verify the JWT appended to HTTP requests the clients share a secret. The secret is normally stored in a text file that both Consensus and Execution clients need access to. &#x20;

There are two possible scenarios:&#x20;

* execution and consensus clients run on the same server (both can read the same local file stored on the Docker host)
* execution and consensus clients run on separate servers (the same file needs to be placed on separate servers)

### Execution and consensus on the same server

In this scenario by default slignode.ethereum role will automatically generate a random secret and configure both clients to use it. In case a of multiple servers, a unique random secret will be generated on each of them. You don't need to do anything and things will just work.&#x20;

In case you want to override this behavior and use a predefined secret (or retrive it from a secrets management solution in your CI/CD pipeline) you can set the "jwt\_secret\_string" variable as show below.

```yaml
jwt_secret_string: "7a813d06b0a08de1f1e70ebbc6b3ec8dc589f62d04d346b4754c9d739b4c648b"
```

Refer to [Generating JWT token](jwt-secret.md#generating-jwt-token) section.

#### Recreating JWT token

By default the role will not change the token after it has been created. This means that if the role is re-run on the same server and the token exists it will remain unchanged. If you wish to have the role generate a new token you can set the below variable to "true":

```yaml
recreate_jwt: true
```

When set to true the token will be re-created on the next run. Don't forget to reset the variable to false, otherwise the token will be re-created every time the role runs. Alternatively, you can delete the JWT secret file from the server and re-run the role. By default the file is located in:

```sh
/opt/blockchain/jwt/jwt.hex
```

The clients need to be restarted in order to pick up the new secret.

Summary of steps to regenerate the secret:

1. Set recreate\_jwt: true
2. Execute the role
3. Restart the clients
4. Unset the recreate\_jwt variable&#x20;

### Execution and consensus on separate servers

In this scenario the "jwt\_secret\_string" variable must be set in your playbook (or group/host vars depending on how you deploy). If the variable is not set, a random secret will be generated on each server. Since they won't match, consensus client will not be able to authenticate to the execution client.&#x20;

```yaml
jwt_secret_string: "7a813d06b0a08de1f1e70ebbc6b3ec8dc589f62d04d346b4754c9d739b4c648b"
```

Refer to [Generating JWT token](jwt-secret.md#generating-jwt-token) section.

### Generating JWT token

The following command can be used to generate a JWT token.&#x20;

```sh
openssl rand -hex 32 | tr -d "\n" > "jwt.hex"
```
