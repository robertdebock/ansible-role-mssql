MSSql
=========

[![Build Status](https://travis-ci.org/robertdebock/ansible-role-mssql.svg?branch=master)](https://travis-ci.org/robertdebock/ansible-role-mssql)

Provides Microsoft SQL Server for your system.

Requirements
------------

- access to either internet (using public repositories) or a private repository containing mssql sofware.

Role Variables
--------------

See default values in defaults/main.yml:
- mssql_add_repositories - Add the repositories?
- mssql_sa_password - The password for the administrator.
- mssql_pid - The type of license to use.

Dependencies
------------

These requirements are described in `requirements.yml`:
- role: robertdebock.bootstrap

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
    - role: robertdebock.bootstrap
    - role: robertdebock.mssql
```

And here is a more customised installation. This:
- bootstraps a system, so Ansible can use it.
- uses /dev/sdb1 for a new partition.
- runs (only) the installation, which excludes the repository setup.
- bind the Linux machine to Active Directory.
- create a keytab file for MSSQL.

```
- hosts: all
  become: true
  gather_facts: no

  vars:
    domain: example.com
    service_account_username: mysrvacct
    service_account_password: Sup3rS3cReT
    mssql_conf:
    - option: forceencryption
      value: 0
      section: network

  handlers:
  - name: restart mssql-server
    service:
      name: mssql-server
      state: restarted

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
      fstype: xfs
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
      mssql_add_repositories: no

  - name: install software required for binding to active directory
    package:
      name: "{{ item }}"
    with_items:
      - realmd
      - krb5-workstation
      - oddjob
      - oddjob-mkhomedir
      - sssd
      - samba-common-tools

  - name: install software required for the ansible expect module
    package:
      name: "{{ item }}"
    with_items:
      - python2-pexpect
      - python2-ptyprocess

  - name: check current domain
    command: realm list --name-only
    register: current_domain
    changed_when: no

  - name: join active directory domain
    expect:
      command: "realm join {{ domain }} -U {{ service_account_username }}@{{ domain }} -v"
      responses:
        (?i)password: "{{ vault_service_account_password }}"
    when:
      - current_domain.stdout != domain

  - name: get kerberos ticket
    expect:
      command: "kinit {{ service_account_username }}@{{ domain|upper }}"
      responses:
        (?i)password: "{{ service_account_password }}"
    changed_when: no

  - name: find key version number
    command: kvno MSSQLSvc/{{ ansible_fqdn }}:1433
    register: kvno
    changed_when: no

  - name: create mssql.keytab
    expect:
      command: ktutil
      responses:
        ktutil:
          - "addent -password -p MSSQLSvc/{{ ansible_fqdn }}:1433@{{ domain|upper }} -k {{ kvno.stdout[-1] }} -e aes256-cts-hmac-sha1-96"
          - "addent -password -p MSSQLSvc/{{ ansible_fqdn }}:1433@{{ domain|upper }} -k {{ kvno.stdout[-1] }} -e rc4-hmac"
          - "wkt /var/opt/mssql/secrets/mssql.keytab"
          - "quit"
        Password: "{{ service_account_password }}"
      creates: /var/opt/mssql/secrets/mssql.keytab
    notify:
      - restart mssql-server

    - name: set permissions on mssql.keytab
      file:
        path: /var/opt/mssql/secrets/mssql.keytab
        owner: mssql
        group: mssql
        mode: 0400

    - name: configure mssql-server
      notify: restart mssql-server
      ini_file:
        path: /var/opt/mssql/mssql.conf
        option: "{{ item.option }}"
        value: "{{ item.value }}"
        section: "{{ item.section }}"
      with_items: "{{ mssql_conf }}"
```

Install this role using `galaxy install robertdebock.mssql`.

License
-------

Apache License, Version 2.0

Author Information
------------------

Robert de Bock <robert@meinit.nl>
