MSSql
=========

[![Build Status](https://travis-ci.org/robertdebock/ansible-role-mssql.svg?branch=master)](https://travis-ci.org/robertdebock/ansible-role-mssql)

Provides Microsoft SQL Server for your system.

Requirements
------------

These requirements are explicitly mentioned in meta/main.yml.
- python-pip

Role Variables
--------------

See defaults/main.yml, but mostly:
- mssql_sa_password - The password for the administrator.
- mssql_pid - The type of license to use.

Dependencies
------------

- robertdebock.python-pip

Download the dependencies by issuing this command:
```
ansible-galaxy install --role-file requirements.yml
```

Example Playbook
----------------

The simplest form:
```
- hosts: servers

  roles:
    - role: robertdebock.mssql
```

And here is a more customised installation. This:
- bootstraps a system, so Ansible can use it.
- uses /dev/sdb1 for a new partition
- runs (only) the installation, which excludes the repository setup.

```
- hosts: all
  become: true
  gather_facts: no

  tasks:
  - name: role bootstrap
    include_role:
      name: robertdebock.bootstrap

  - name: create volume group
    lvg:
      pvs: /dev/sdb1
      vg: data

  - name: create logical volume
    lvol:
      vg: data
      lv: mssql
      size: 10240

  - name: create filesystem
    filesystem:
      fstype: ext3
      dev: /dev/data/mssql

  - name: mount filesystem
    mount:
      path: /var/opt/mssql
      src: /dev/data/mssql
      fstype: ext3
      state: present

  - name: role mssql
    include_role:
      name: robertdebock.mssql
    vars:
      mssql_sa_password: SoMeComPl3xP@sSWord
      tags:
        - installation
```

Install this role using `galaxy install robertdebock.mssql`.

License
-------

Apache License, Version 2.0

Author Information
------------------

Robert de Bock <robert@meinit.nl>
