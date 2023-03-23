# Security

slingnode.ethereum role is designed to seamlessly integrate into existing workflows. It does not   modify any settings not directly related to its functionality. Therefore it is up to the role user to secure Docker engine and the host OS.&#x20;

slingnode.ethereum comes with secure defaults for configuration within its scope. The security related configuration settings are outlined below:&#x20;

* Containers run under non-privileged user accounts
* Each container runs under a dedicated user account
* Container users have permission only to the directories bound to the containers
* Containers are started with CIS Docker Benchmark v1.5 recommended  settings

```yaml
    restart: on-failure:5
    read_only: true
    tmpfs:
      - /run
      - /tmp
    security_opt:
      - no-new-privileges
```

* By default ports are not exposed to the host (except P2P ports)
* Host firewall is enabled by default and allows only P2P and SSH ports

## Host Firewall

Be default host firewall will be installed and enabled for both RedHat and Debian based operating systems.  The role will configure firewall rules for the client that is being deployed.&#x20;

SSH will be explicitly allowed from any sources. This is to prevent situation where you get locked out of your server. &#x20;

If you run your server on a privet network (behind a firewall or a security group) you can disable the firewall all together.  The below variables control what the role does with the host firewall.&#x20;

```yaml
# When enabled the role will install firewall package and add the required rules
enable_firewall: true

# when enable_firewall: true, ssh port will be allowed, override this variable if you use a custom port
ssh_port: 22

# Set to false to not allow SSH - you may be locked out of your server
allow_ssh: true
```

## Servers connected directly to the internet&#x20;

Please make sure to review the [Docker & host firewall ](docker-and-host-firewall.md)section to understand how Docker manipulates the  firewall rules.&#x20;
