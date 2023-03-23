# Known issues

## python-requests installed using RPM package

The role has been tested with various OS images of the [supported operating systems](overview/supported-oses.md).  On some AlmaLinux 9.1 images the roles fails on the "Install required Python modules" task with the error shown below:&#x20;

```
ERROR: Cannot uninstall requests 2.25.1, RECORD file not found. Hint: The package was installed by rpm.
```

This occurs because an older version of the "requests" module was installed using the OS package manager and PIP is not able to upgrade it. To fix this issue the RPM package needs to be removed.&#x20;

```sh
sudo yum remove python3-requests
```

This can incorporated into the playbook using a pre\_task which would run before the role, for example:&#x20;

```yaml
  pre_tasks:

    - name: Remove python3-requests
      ansible.builtin.package:
        name: python3-requests
        state: absent
```

## Checkpoint sync public endpoint availability

This issue is listed here for completeness. It is described in the [Checkpoint sync](checkpoint-sync.md#public-endpoint-availability) section of the documentation.
