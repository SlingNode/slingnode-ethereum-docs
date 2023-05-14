# Role variables

The variable naming and layout is the same as in the [slingnode.ethereum](role-variables.md) role. This is a deliberate design choice which enables seamless integration of both roles and full node deployment in a single Playbook.

Role variables are defined in 'defaults'. This means they have the lowest precedence and can be easily overridden. See [Ansible documentation](https://docs.ansible.com/ansible/latest/playbook\_guide/playbooks\_variables.html#understanding-variable-precedence) for details on the precedence.
