# Docker & host firewall

Running Docker on a server that is directly exposed to the internet (has its own public IP address) poses risks that are not obvious. This page explains what they are.

In short, when a container port is mapped to the 0.0.0.0 ("all IPs") address of the host, it is by default accessible to the whole network (LAN or the internet in case of servers with public IPs) regardless of what host firewall rules have been configured on the host. <mark style="color:red;">This means that if you enable host firewall and set default deny rule, it will not block connections to container ports mapped to host's 0.0.0.0 address</mark>.&#x20;

[Docker documentation](https://docs.docker.com/network/iptables/) describes why this is and how Docker manipulates Iptables.

In addition to Docker documentation there are many articles describing this issue and potential solutions. We have tested many of them and have not found one that we would feel comfortable including in the role - if you do have a good solution please submit a pull request.  &#x20;

This is a problem you need to be aware of if you run a node on a server directly exposed to the internet (which is the case for most bare metal servers).&#x20;

By default slingnode.ethereum - with exception of the P2P ports - **does not expose any ports to the host**. When you choose to do so they will be mapped to the host's 127.0.0.1 IP address and therefore not accessible from outside of the server itself (regardless of the host firewall configuration). Refer to [Exposing ports](exposing-ports.md) page for details.  &#x20;

Unless your security policy dictates the use of host firewall, this is not a problem when you run nodes on private networks isolated by a network firewall or security groups.&#x20;
