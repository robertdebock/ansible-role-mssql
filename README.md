# [mssql](#mssql)

Install and configure mssql on your system.

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-mssql.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-mssql)|[![github](https://github.com/robertdebock/ansible-role-mssql/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-mssql/actions)|[![quality](https://img.shields.io/ansible/quality/24094)](https://galaxy.ansible.com/robertdebock/mssql)|[![downloads](https://img.shields.io/ansible/role/d/24094)](https://galaxy.ansible.com/robertdebock/mssql)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-mssql.svg)](https://github.com/robertdebock/ansible-role-mssql/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.mssql
```

The machine needs to be prepared in CI this is done using `molecule/resources/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.core_dependencies
    - role: robertdebock.ca_certificates
    - role: robertdebock.microsoft_repository_keys
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for mssql

# mssql_add_repositories can be used to select if you want the repositories installed by this role.
# See vars/main.yml for the location of the repositories. Can be: yes, true or 1.
mssql_add_repositories: yes

# What version to use, currently either 2017 or 2019.
# `2017` is the only working version now, `2019` lacks the required
# mssql-server-agent package.
mssql_version: "2017"

# Select the version of server and server_agent to install.
mssql_server_version: 14.0.3294.2-27
mssql_server_agent_version: 14.0.3015.40-1

# mssql_sa_password contains the password for a system administrator.
# The password must be at least 8 characters long and contain characters from
# three of the following four sets:
# - uppercase letters
# - lowercase letters
# - numbers
# - and symbols
mssql_sa_password: "StR0nGp4ss."

# mssql_pid refers to the product key to use. Either:
# - Evaluation
# - Developer
# - Express
# - Web
# - Standard
# - Enterprise
# - A product key (Format: #####-#####-#####-#####-#####)
mssql_pid: Evaluation

# To enable full text search, set this value to yes.
mssql_fts: no
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

## [Status of requirements](#status-of-requirements)

| Requirement | Travis | GitHub |
|-------------|--------|--------|
| [robertdebock.bootstrap](https://galaxy.ansible.com/robertdebock/bootstrap) | [![Build Status Travis](https://travis-ci.com/robertdebock/ansible-role-bootstrap.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-bootstrap) | [![Build Status GitHub](https://github.com/robertdebock/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-bootstrap/actions) |
| [robertdebock.ca_certificates](https://galaxy.ansible.com/robertdebock/ca_certificates) | [![Build Status Travis](https://travis-ci.com/robertdebock/ansible-role-ca_certificates.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-ca_certificates) | [![Build Status GitHub](https://github.com/robertdebock/ansible-role-ca_certificates/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-ca_certificates/actions) |
| [robertdebock.core_dependencies](https://galaxy.ansible.com/robertdebock/core_dependencies) | [![Build Status Travis](https://travis-ci.com/robertdebock/ansible-role-core_dependencies.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-core_dependencies) | [![Build Status GitHub](https://github.com/robertdebock/ansible-role-core_dependencies/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-core_dependencies/actions) |
| [robertdebock.microsoft_repository_keys](https://galaxy.ansible.com/robertdebock/microsoft_repository_keys) | [![Build Status Travis](https://travis-ci.com/robertdebock/ansible-role-microsoft_repository_keys.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-microsoft_repository_keys) | [![Build Status GitHub](https://github.com/robertdebock/ansible-role-microsoft_repository_keys/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-microsoft_repository_keys/actions) |

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/mssql.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|el|7|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.

## [Exceptions](#exceptions)

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| alpine | Microsoft has [limited distribution support](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017). |
| debian | Microsoft has [limited distribution support](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017). |
| fedora | Microsoft has [limited distribution support](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017). |
| opensuse | Microsoft has [limited distribution support](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017). |
| ubuntu | Microsoft has [limited distribution support](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017). |
| amazonlinux | Microsoft has [limited distribution support](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017). |
| centos:8 | nothing provides python needed by mssql-server-14.0.3356.20-23.x86_64 |

## [Included version(s)](#included-versions)

This role [refers to a version](https://github.com/robertdebock/ansible-role-mssql/blob/master/defaults/main.yml) released by Microsoft. Check the released version(s) here:
- [SQL for RHEL](https://packages.microsoft.com/rhel/7/mssql-server-2017/).
- [SQL for Ubuntu](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2017/pool/main/m/mssql-server/).
- [SQL for SLES](https://packages.microsoft.com/sles/12/mssql-server-2017/).

This version reference means a role may get outdated. Monthly tests occur to see if [bit-rot](https://en.wikipedia.org/wiki/Software_rot) occured. If you however find a problem, please create an issue, I'll get on it as soon as possible.
## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-mssql) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-mssql/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## [License](#license)

Apache-2.0


## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
