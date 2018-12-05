[![Build Status](https://travis-ci.org/kleinstuff/ansible-zabbix-agent.png)](https://travis-ci.org/kleinstuff/ansible-zabbix-agent)

kleinstuff.ipsec
=========

Install ipsec (LibreSwan)/
Currently supports:
* CentOS/RHEL 7

Requirements
------------

No extra requirements needed.

Role Variables
--------------

Almost everything is setup by defaults/main.yml (you can check there).
But you need to set at least 1 connection (there is a fake one in defaults/main.yml.

Dependencies
------------

Setup a vault password, you can do it by:
* Setting up a default vault-password in ansible.cfg with:
```
    vault_password_file = ./.vaultpass
```
* Adding a password to it:
```
    echo "123" > .vaultpass
```

Example Playbook
----------------

```
    - hosts: all
      roles:
         - { role: kleinstuff.ipsec}
```
License
-------

GPL

Author Information
------------------

If you want to suggest changes or request new features, please feel free to create a issue or send a pull request.
