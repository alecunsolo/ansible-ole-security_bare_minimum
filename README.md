[![build status](https://github.com/alecunsolo/ansible-role-security_bare_minimum/actions/workflows/ci.yml/badge.svg)](https://github.com/alecunsolo/ansible-role-security_bare_minimum/actions/workflows/ci.yml)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://github.com/pre-commit/pre-commit)

Ansible Role: security_bare_minimum
=========

Minimal security configuration. You can setup `ssh` and `firewalld`. I use [FreeIPA](https://www.freeipa.org) for user management and `sudo` access, so this role won't setup such things.

**WARNING** You can lock yourself out (changing ssh port or misconfiguring firewalld) and have no more root access (if you choose to disable root password). Make sure to have a plan b.

Requirements
------------

None.

Role Variables
--------------

```yaml
security_lock_root: true
```
Whether to lock the root user password or not.
```yaml
security_ssh_enabled: true
security_ssh_template: sshd_default_template
security_ssh_port: 22
security_ssh_permit_root_login: prohibit-password
security_ssh_pwd_auth: 'no'
```
Basic `ssh` configuration. The options are added to a custom file included by the main `sshd` config. By default a basic template [sshd_default_template](templates/sshd_default_template) is used, but it can be changed to suit your needs.
```yaml
security_firewall_enabled: true
security_firewalld_services: []
# - src: custom/file/directory/file.txt
#   name: service.xml

security_firewalld_config: []
# - zone_name: dmz
#   interfaces:
#     - name: eth0
#       state: present
#   services:
#     - name: syncthing
#       state: enabled
#     - name: ssh
#       state: disabled
#   ports:
#     - name: 8090/tcp
#       state: enabled
# - zone_name: public
#   services:
#     - name: dns
#       state: enabled
#     - name: https
#       state: disabled
#   ports:
#     - name: 8080/tcp
#       state: enabled
```
Basic `firewalld` configuration: you can add custom service definitions and configure some `zone` parameters (interfaces, services and ports).

Dependencies
------------

`firewalld` is configured using the module `ansible.posix.firewalld`: you need to be sure to have the collection installed on the management host.

Example Playbook
----------------
```yaml
- name: Security
  hosts: all
  vars:
    security_firewalld_services:
      - src: unifi.xml
        name: unifi.xml
  tasks:
    - name: Include security_bare_minimum.
      ansible.builtin.include_role:
        name: alecunsolo.security_bare_minimum
```

License
-------

MIT

Notes
-----

Testing with molecule is ~~stolen from~~ heavily inspired by [Jeff Geerling](https://www.jeffgeerling.com/). Watch [his video](https://youtu.be/FaXVZ60o8L8) (and the other ones as well).
