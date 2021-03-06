eNiXHosting.proxmox-ve
=================

A role for deploying and configuring [Proxmox VE](https://www.proxmox.com/en/proxmox-ve) on unix hosts using [Ansible](http://www.ansible.com/).
This role is really tied to eNiXHosting usage by configuring local LVM volumes, ISCSI multi-path, etc... So it is maybe not relevant to everyone, however every step is configurable so can be used independently


Requirements
------------

Supported targets:

- Debian 8 "Jessie"
- Debian 9 "Stretch"


Role Variables
--------------

This roles comes preloaded with almost every available default. You can override each one in your hosts/group vars, in your inventory, or in your play. See the annotated defaults in `defaults/main.yml` for help in configuration. All provided variables start with `proxmox_ve__`.

- `proxmox_ve__enterprise` - enable or not the enterprise subscription for Proxmox VE. default: false.
- `proxmox_ve__net_ovs` - enable OpenVswitch network configuration on host, default: false.
- `proxmox_ve__net_template` - template used for `/etc/network/interfaces` configuration on the host, default: interfaces.j2. The path can be either changed or overloaded in your playbook. The default template only provide a basic bridge configuration.
- `proxmox_ve__storage_lvm` - description of lvm storage to initialise and configure in proxmox. exemple configuration above.
- `proxmox_ve__storage_iscsi` - description of iscsi storage to configure in proxmox. exemple configuration above.
- `proxmox_ve__storage_iscsi_multipath_template` - template file to use for multipath configuration.


Dependencies
------------

- None

Usage
-----

Clone this repo into your roles directory:

    $ git clone https://gitlab.enix.org/ansible/ansible-proxmox-ve.git roles/proxmox-ve

Or use Ansible galaxy requirements.yml

    # public role
    - src: eNiXHosting.proxmox-ve
      name: proxmox-ve

And add it to your play's roles:

    - hosts: all
      roles:
        - role: eNiXHosting.proxmox-ve
          proxmox_ve__storage_iscsi:
            - name: iscsi-storage
              portal: 192.168.0.1
              target: iqn.2015-11.com.storage:iscsi.12315132
              volumes:
                - name: bigvolume
                  wwid: 3600c0ff0003bb7fcb730e75a01000000
          proxmox_ve__storage_lvm:
            - name: "localvm"
              devices:
                - /dev/md12
              pesize: "128"
              shared: 0
            - name: "iscsilvm"
              devices:
                - /dev/mapper/bigvolume
              pesize: "256"
              shared: 1

You can also use the role as a playbook. You will be asked which hosts to provision, and you can further configure the play by using `--extra-vars`.

    $ ansible-playbook -i inventory --extra-vars='{...}' main.yml

Still to do
-----------

- auto add hosts to clusters
- manage users and credentials
- change storage configuration to usage of pvesm commands


Changelog
---------

### 1.0

First version that include basic storage configuration.

License
-------

GPLv2

Author Information
------------------

Laurent CORBES <laurent.corbes@enix.fr> - http://www.enix.fr
