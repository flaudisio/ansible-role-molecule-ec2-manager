# Ansible Role: Molecule EC2 Manager

Ansible role for managing Molecule [EC2 instances][molecule-ec2].

[molecule-ec2]: https://molecule.readthedocs.io/en/stable/configuration.html#ec2

## Requirements

- Molecule

_Please be aware this is not a "standalone" role: it **must be used** as a
[Molecule dependency][molecule-dependency]!_

See the [usage section](#usage) for more details.

[molecule-dependency]: https://molecule.readthedocs.io/en/stable/configuration.html#dependency

## Role Variables

This is a non-exhaustive list of the role variables. Please see the file [`defaults/main.yml`](defaults/main.yml)
for more details.

```yaml
mec2_aws_region: us-east-1
```

The AWS region where instances will be deployed. If not set, it will use `AWS_REGION`
or `EC2_REGION` (in this order of precedence).

```yaml
mec2_action: create
```

Whether to create or destroy the instance(s). Allowed values: `prepare`, `create` or `destroy`.

```yaml
mec2_distro: ubuntu1804
```

Distribution name as defined in [`vars/distros.yml`](vars/distros.yml). It's used
to discover which AMI will be used for each test instance.

```yaml
mec2_instance_type: t3.micro
```

Default instance type. You can also set `instance_type` as a [platform configuration](#molecule-configuration).

```yaml
mec2_vpc_subnet_id: subnet-abc1234
```

A VPC **public** subnet where the instance(s) will be allocated. If not set, we'll
do our best to find a suitable subnet.

## Dependencies

None.

## Usage

### Requirements file

File: `<role_dir>/molecule/default/requirements.yml`:

```yaml
---
- src: https://github.com/mandic-labs/ansible-role-molecule-ec2-manager.git
  scm: git
  version: master
  name: molecule-ec2-manager
```

### Molecule configuration

File: `<role_dir>/molecule/default/molecule.yml`:

```yaml
---
dependency:
  name: galaxy
driver:
  name: ec2
platforms:
  - name: instance
    distro: ubuntu1804
# ...
```

### `Prepare` playbook

File: `<role_dir>/molecule/default/prepare.yml`:

```yaml
---
- name: Prepare
  hosts: all
  gather_facts: false

  roles:
    - role: molecule-ec2-manager
      vars:
        mec2_action: prepare
```

### `Create` playbook

File: `<role_dir>/molecule/default/create.yml`:

```yaml
---
- name: Create
  hosts: localhost
  connection: local
  gather_facts: false

  roles:
    - role: molecule-ec2-manager
      vars:
        mec2_vpc_subnet_id: subnet-123456
```

### `Destroy` playbook

File: `<role_dir>/molecule/default/destroy.yml`:

```yaml
---
- name: Destroy
  hosts: localhost
  connection: local
  gather_facts: false

  roles:
    - role: molecule-ec2-manager
      vars:
        mec2_action: destroy
```

### Running Molecule

```console
$ cd /path/to/my/role

$ export AWS_ACCESS_KEY_ID=<ACCESS_KEY>
$ export AWS_SECRET_ACCESS_KEY=<SECRET_KEY>
$ export EC2_REGION=us-east-1  # optional

$ molecule test
```

## TODO

- Allow launching spot instances

## License

[MIT](LICENSE).
