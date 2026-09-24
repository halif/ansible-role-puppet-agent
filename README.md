# ansible-role-puppet-agent

[![CI](https://github.com/ildar/ansible-role-puppet-agent/actions/workflows/ci.yml/badge.svg)](https://github.com/ildar/ansible-role-puppet-agent/actions/workflows/ci.yml)
[![Molecule](https://img.shields.io/badge/tested%20with-Molecule-blueviolet)](https://molecule.readthedocs.io/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

Ansible role for installing and configuring the **Puppet Agent** on Ubuntu and Red Hat family Linux systems.

The role currently supports:

- Ubuntu 20.04 (Focal)
- Ubuntu 22.04 (Jammy)
- RHEL-family systems with major versions 8 and 9
- Rocky Linux 9 is covered by Molecule integration tests

The role installs Puppet Agent 7, configures `puppet.conf`, manages the Puppet service and optionally creates a system-wide `puppet` command symlink.

---

## Requirements

- Ansible Core
- Supported operating system:
  - Ubuntu 20.04
  - Ubuntu 22.04
  - RHEL-family 8
  - RHEL-family 9
- Internet access to the official Puppet package repositories
- Root privileges on the managed host

---

## Role Variables

Default variables are defined in `defaults/main.yml`.

### Puppet version

```yaml
puppet_version: "7"
```

### Puppet Agent package and service

```yaml
puppet_agent_package_name: "puppet-agent"
puppet_agent_service_name: "puppet"
```

### Puppet configuration

```yaml
puppet_conf_dir: "/etc/puppetlabs/puppet"
puppet_conf_file: "/etc/puppetlabs/puppet/puppet.conf"

puppet_server: "puppet"
puppet_server_port: 8140
puppet_agent_certname: "{{ ansible_fqdn }}"
puppet_environment: "production"
```

### Supported Ubuntu releases

```yaml
puppet_supported_ubuntu_releases:
  - focal
  - jammy
```

### Supported Red Hat family versions

```yaml
puppet_supported_redhat_major_versions:
  - "8"
  - "9"
```

### Repository management

```yaml
puppet_repository_url: "https://apt.puppet.com"
puppet_repository_component: "puppet7"
puppet_yum_repository_url: "https://yum.puppet.com"

puppet_manage_repository: true
puppet_apt_update: true
puppet_dnf_update: true
```

The role automatically selects the appropriate repository configuration according to the target operating system.

### Service management

```yaml
puppet_manage_service: true
puppet_service_enabled: true
puppet_service_state: "started"
```

### Puppet command symlink

```yaml
puppet_manage_command_symlink: true
puppet_command_symlink: "/usr/local/bin/puppet"
puppet_command_path: "/opt/puppetlabs/bin/puppet"
```

When enabled, the role creates:

```text
/usr/local/bin/puppet -> /opt/puppetlabs/bin/puppet
```

---

## Example

```yaml
---
- name: Configure Puppet Agent
  hosts: all
  become: true

  roles:
    - role: ildar.puppet_agent
```

Example with custom settings:

```yaml
---
- name: Configure Puppet Agent
  hosts: all
  become: true

  roles:
    - role: ildar.puppet_agent
      vars:
        puppet_server: "puppet.example.com"
        puppet_server_port: 8140
        puppet_environment: "production"
```

---

## What the role does

The role performs the following tasks:

1. Validates the target operating system.
2. Configures the official Puppet package repository.
3. Installs the required package-management dependencies.
4. Installs Puppet Agent.
5. Configures `/etc/puppetlabs/puppet/puppet.conf`.
6. Enables and starts the Puppet service.
7. Optionally creates `/usr/local/bin/puppet`.
8. Verifies the resulting system state with Molecule.

---

## Operating System Support

### Ubuntu

Ubuntu systems use the Puppet APT repository.

Supported releases:

```text
Ubuntu 20.04 (Focal)
Ubuntu 22.04 (Jammy)
```

### Red Hat family

Red Hat family systems use the Puppet YUM/DNF repository.

Supported major versions:

```text
8
9
```

Rocky Linux 9 is currently used as the RHEL-family integration-test platform in Molecule.

Unsupported operating systems or unsupported releases fail during the repository configuration stage instead of continuing with an incompatible installation.

---

## Testing

The role is tested with [Molecule](https://molecule.readthedocs.io/) and Docker.

Current Molecule scenarios:

```text
molecule/
├── default/
│   ├── Dockerfile
│   ├── molecule.yml
│   ├── converge.yml
│   └── verify.yml
│
└── rocky9/
    ├── Dockerfile
    ├── molecule.yml
    ├── converge.yml
    └── verify.yml
```

The `default` scenario tests Ubuntu.

The `rocky9` scenario tests Rocky Linux 9.

The Molecule verification stage checks that Puppet Agent is installed and configured correctly, including the Puppet service, configuration directory, `puppet.conf`, executable and command symlink.

---

## Continuous Integration

GitHub Actions runs the following pipeline:

```text
                   GitHub
                      │
                      ▼
                    Lint
                 ┌────┴────┐
                 │         │
             yamllint   ansible-lint
                 │         │
                 └────┬────┘
                      │
                      ▼
                  Molecule
                 ┌────┴────┐
                 │         │
                 ▼         ▼
               Ubuntu    Rocky 9
               default    rocky9
```

CI runs on every branch push, pull requests and manual workflow dispatch.

For each Molecule scenario the workflow performs:

```text
syntax
   ↓
create
   ↓
converge
   ↓
verify
   ↓
destroy
```

If `converge` fails, the workflow collects Docker and Puppet diagnostics before destroying the test container.

---

## Project Structure

```text
ansible-role-puppet-agent/
├── .config/
│   ├── ansible-lint.yml
│   └── yamllint.yml
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── defaults/
│   └── main.yml
│
├── handlers/
│   └── main.yml
│
├── meta/
│   └── main.yml
│
├── molecule/
│   ├── default/
│   │   ├── Dockerfile
│   │   ├── molecule.yml
│   │   ├── converge.yml
│   │   └── verify.yml
│   │
│   └── rocky9/
│       ├── Dockerfile
│       ├── molecule.yml
│       ├── converge.yml
│       └── verify.yml
│
├── tasks/
│   ├── main.yml
│   ├── repository.yml
│   ├── install.yml
│   ├── configure.yml
│   └── service.yml
│
├── templates/
│   └── puppet.conf.j2
│
├── .gitignore
├── LICENSE
└── README.md
```

---

## Development

Lint the role locally:

```bash
yamllint -c .config/yamllint.yml .
ansible-lint -c .config/ansible-lint.yml .
```

Run a specific Molecule scenario:

```bash
molecule test --scenario-name default
```

or:

```bash
molecule test --scenario-name rocky9
```

Molecule integration tests are primarily executed by GitHub Actions.

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE).
