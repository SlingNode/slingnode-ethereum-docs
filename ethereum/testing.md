# Testing

slingnode.ethereum roles ships with a comprehensive suite of infrastructure tests using [Anisble Molecule](https://molecule.readthedocs.io/en/latest/) project. Ansible Molecule is designed to aid in testing Ansible roles (it can be used to test full Playbooks too). You can easily use the included tests to see what the role does and how it behaves with different variables. You can also copy and adapt the tests to your own infrastructure to aid in your playbook development - whether or not using slingnode.ethereum role.

This page is meant to provide overview of the included test scenarios and how to get going with it. It is not meant as a Molecule's user guide. Please refer to the [official docs](https://molecule.readthedocs.io/en/latest/) if required.&#x20;

## Test summary

The table below outlines the summary of the test scenarios included with the role. The "purpose" column is only a high-level description, there are a lot of role variable combinations tested in each scenario. &#x20;

| Scenario name             | Driver/provider | Purpose                                  |
| ------------------------- | --------------- | ---------------------------------------- |
| default                   | Vagrant/Libvirt | Test defaults against all supported OSes |
| default\_single\_instance | Vagrant/Libvirt | Test defaults against ad-hoc OS          |
| expose\_ports             | Vagrant/Libvirt | Test mapping ports to the host           |
| jwt                       | Vagrant/Libvirt | Test predefined JWT                      |
| multi\_tier               | Vagrant/Libvirt | Test distributed deployment              |
| default\_docker           | Docker          | Test defaults against d-hoc OS in Docker |

## Molecule drivers

Molecule supports various "drivers" to create and manage instances during test lifecycle. The easiest one to use (and the default for Molecule) is Docker. The only requirement for using Docker driver is a running Docker daemon. Sadly, this does not work well for slingnode.ethereum role. The role uses Docker to manage client lifecycle which means that the tests have to run Docker containers in Docker containers (DIND). While this works, it requires specially prepared Docker images which are not readily available.&#x20;

slingnode.ethereum test scenarios use Vagrant as the "Molecule driver" and Libvirt/KVM as the "Vgrant provider". This enables the tests to run in full unmodified VMs with widely available OS images for all supported platforms. The downside is that the Vagrant / Libvirt set up is more involved and not as readily available in CI systems as Docker. For this reason a single Docker based Molecule scenario is shipped with the role - "default\_docker".&#x20;

### default\_docker scenario

This scenario uses rockylinux8 **** image. Rocky8 is the only docker image we identified that was not causing issues.

### Set up

This section outlines high-level steps to setup up and execute Molecule test suite:

For Docker&#x20;

1. follow steps for your OS to install Docker engine
2. [Install Molecule](https://molecule.readthedocs.io/en/latest/installation/)

For Vagrant:

1. Install Libvirt
2. Install Vagrant
3. [Install Molecule](https://molecule.readthedocs.io/en/latest/installation/)

The installation and configuration of the per-requisites is outside of the scope of this documentation, there are many guides on the internet that explain how to set them up.&#x20;

## Functional testing

Molecule executes a set of user defined tests running "verify.yml" playbook during the "verify" stage.  This playbook includes a set of Ansible tasks that inspect the state of the instances after the role has run (converge stage). This allows for ascertaining that the instances are in expected state (the role did what it was supposed to based on the variables). The tests included with the role range from simple checking if containers are running all the way to making Ethereum API calls and ensuring the clients are in working condition. Check "verify.yml" playbook in each scenario to see the test tasks.&#x20;

## Role dependencies

Molecule will install required dependencies during the "prepare" stage. Those are defined in the "prepare.yml" playbook.

## Resources&#x20;

In order to keep the tests DRY and ease the maintenance, various playbooks and test tasks are re-used where appropriate.&#x20;

```
resources/
├── playbooks    # Playbooks used by the provisioner
├── scripts      # utility scripts 
└── tests        # re-usable verify tasks
```

## Running tests

Molecule configuration options support environment [variable substitution](https://molecule.readthedocs.io/en/latest/configuration/#variable-substitution). Any value defined in molecule.yml can be defined as env var. This makes the tests flexible and helps to keep them DRY (Don't Repeat Yourself). This enables us to define a single scenario and pass OS platform or Ethereum client names as variables to the test. slingnode.ethereum test suite heavily relies on this feature.  The following environment variables can be used:

```sh
SLINGNODE_EXECUTION
SLINGNODE_CONSENSUS
SLINGNODE_VALIDATOR
SLINGNODE_BOX
```

### Test using Docker driver - default\_docker&#x20;

```sh
SLINGNODE_EXECUTION=geth SLINGNODE_CONSENSUS=lighthouse SLINGNODE_VALIDATOR=lighthouse molecule test -s default_docker
```

The clients to test can be specified using SLINGNODE\_ variables. To test all clients (not all combinations though) use the following script:

```sh
molecule/resources/scripts/default_docker.sh
```

### Test all supported OSes in parallel - default

```sh
SLINGNODE_EXECUTION=geth SLINGNODE_CONSENSUS=lighthouse SLINGNODE_VALIDATOR=lighthouse molecule test -s default
```

To test all clients (not all combinations though)  against all supported OSes use the following script:

```sh
molecule/resources/scripts/default.sh
```

### Test specific clients and specific OS

The OS to test can be specified using SLINGNODE\_BOX variable. This can be any Vagrant box that supports Libvirt provider. For example:&#x20;

```sh
SLINGNODE_BOX=almalinux/9 SLINGNODE_EXECUTION=nethermind SLINGNODE_CONSENSUS=prysm SLINGNODE_VALIDATOR=prysm molecule test -s multi_tier
```

## Inspecting state of test instance during test lifecycle

Molecule enables us to execute [each stage separately](https://molecule.readthedocs.io/en/latest/getting-started/#run-test-sequence-commands). This is very useful during development or anytime we want to inspect the state of an instance manually by logging in to the instance.&#x20;

```sh
# Create and prepare instance 
molecule create -s expose_ports

# Execute slingnode.ethereum role with defined set of variables
molecule converge -s expose_ports

# Login to the instance
molecule login -s expose_ports 

# Execute verify tests
molecule verify -s expose_ports

# Destroy instance
molecule destroy -s expose_ports
```
