Ansible Role: iSCSI Initiator
=========

This role configure a linux server as iSCSI initiator using `open-iscsi`.

This role introduces a modification into [open-iscsi community module](https://docs.ansible.com/ansible/latest/collections/community/general/open_iscsi_module.html) to enable mutual authentication. Modified module is located in `library/open_iscsi.py`.


Requirements
------------

None

Role Variables
--------------

Available variables are listed below along with default values (see `defaults\main.yaml`)
To configure the iSCSI initiator, the following variables need to be configured:

- iSCSI initiator iqn

```yml
open_iscsi_initiator_name: "iqn.2021-07.com.ricsanfre:{{ ansible_facts['nodename'] }}"
```

- Configuration of iSCSI daemon. Automatic login and authentication configuration

```yml
# open-iscsi configuration
open_iscsi_initiator_name: iqn.2021-07.com.ricsanfre:iscsi-initiator
open_iscsi_authentication: true
open_iscsi_auth_username: iqn.2021-07.com.ricsanfre:iscsi-initiator
open_iscsi_auth_password: s1cret0
open_iscsi_auth_username_in: iqn.2021-07.com.ricsanfre:iscsi-target
open_iscsi_auth_password_in: 0tr0s1cret0
open_iscsi_automatic_startup: true
```
> NOTE: Other parameters of iscsid are available to be configured

- iSCSI Targets to connect to

```yml
open_iscsi_targets:
  - name: 'Target'
    discover: true
    auto_portal_startup: true
    auto_node_startup: true
    portal: '196.168.0.1'
    target: 'iqn.2001-04.org.example:target'
    login: true
```


Dependencies
------------

None.

Example Playbook
----------------

This example use `ricsanfre.storage` role for creating the logical volumes used to configure the iSCSI target.

The example also shows how to use specific authentication method and credentials per target.

```yml
- name: Configure iscsi-client
  hosts: iscsi-initiator
  become: true
  gather_facts: true
  vars:
    open_iscsi_authentication: true
    open_iscsi_automatic_startup: true
    open_iscsi_targets:
      - name: iscsi-target
        discover: true
        auto_portal_startup: true
        auto_node_startup: true
        portal: 192.168.0.11
        target: iqn.2021-07.com.ricsanfre:iscsi-target
        login: true
        node_auth: CHAP
        node_user: iqn.2021-07.com.ricsanfre:iscsi-initiator
        node_pass: s1cret0
        node_user_in: iqn.2021-07.com.ricsanfre:iscsi-target
        node_pass_in: 0tr0s1cret0
  roles:
    - ricsanfre.iscsi_initiator
```

License
-------

MIT

Author Information
------------------

Created by Ricardo Sanchez (ricsanfre)
