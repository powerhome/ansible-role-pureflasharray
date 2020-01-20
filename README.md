Pure FlashArray
===============

Configure Pure FlashArray.

To download the role you can use `ansible-galaxy`. For that create a file `requirements.yml` with the following content:

```yaml
---
- src: https://github.com/powerhome/ansiblerole-purefa
  version: master
  name: powerhome.purefa
```

Afte that run the command:
```
ansible-galaxy install -r requirements.yml
```

PS: This role isn't tested with molecule since there isn't a way to provision a test environment. Molecule was used only to create the directory hierarchy.

Requirements
------------

* `pipenv`

Execute the following to install dependencies:
```sh
pipenv install
pipenv shell
ansible-galaxy collection install -r requirements.yaml
```

Role Variables
--------------

### Array basic config
There are a few basic configs that can be set in the array:

| Variable | required | type | default | description |
| -------- | -------- | ---- | ------- | ----------- |
| purefa_url                  | **Y** | str |  | Array url. |
| purefa_api_token            | **Y** | str |  | Array api token. |
| purefa_array_name           | N | str |  | Name of the array. Must conform to correct naming schema. |
| purefa_phonehome_state      | N | str |  | Define state of phonehome. |
| purefa_remote_assist_state  | N | str |  | Define state of remote assist. |
| purefa_ntp_servers          | N | list |  | A list of up to 4 alternate NTP servers. These may include IPv4, IPv6 or FQDNs. Invalid IP addresses will cause the module to fail. No validation is performed for FQDNs. If more than 4 servers are provided, only the first 4 unique nameservers will be used. |
| purefa_domain               | N | str |  | Domain suffix to be appended when perofrming DNS lookups. |
| purefa_dns_servers          | N | list |  | List of up to 3 unique DNS server IP addresses. These can be IPv4 or IPv6 - No validation is done of the addresses is performed. |

### Local users
To manage local user use the variable `purefa_users`

```yml
purefa_users:
- name: pureuser
  password: password
  state: present
```

where you can set the following values:

| Variable | required | type | default | description |
| -------- | -------- | ---- | ------- | ----------- |
| purefa_users[].name         | **Y** | str |  | The name of the local user account. |
| purefa_users[].password     | **Y** | str |  | Password for the local user. |
| purefa_users[].old_password | N | str |  | If changing an existing password, you must provide the old password for security. |
| purefa_users[].role         | N | str |  | Sets the local user's access level to the array. |
| purefa_users[].state        | N | str | present | Create, delete or update local user account. |


### Directory service

It is possible to configure the array directory service with these variables:

```yml
purefa_ldap_bind_password: ldap_bind_pass
purefa_ldap:
  enable: true
  uri: "ldap://ldap.example.com"
  base_dn: "DC=example,DC=com"
  bind_user: "CN=reader,DC=example,DC=com"
  group_base: "OU=groups"
  aa_group: "pureAdmins"
  sa_group: "storage_admin"
  oa_group: "ops_admin"
  ro_group: "readonly"
```

### SNMP
Configure SNMP:

```yml
purefa_snmp:
- name: public
  community: pws
  host: 0.0.0.0
  state: present
```

### Syslog
Configure Syslog:

```yml
purefa_syslog:
  state: present
  address: syslog.example.com
  port: 1514
  protocol: udp #tcp, tls, udp
```

### SMTP
Configure SMTP:

```yml
purefa_smtp:
  state: present
  user: "user"
  password: "password"
  relay_host: mail.example.com:587
```

### Email alerts
Configure email alerts:

```yml
purefa_alerts:
- name: "flasharray-alerts@purestorage.com"
  enabled: true
- name: "email@example.com"
  enabled: true
```

### Replication connection
Manage replication connections between two FlashArrays:

```yml
purefa_connections:
- target_url: flasharray01.example.com
  connection: async
  target_api: api_token
```


### Volumes
You can use the variable `purefa_volumegroups` to manage:
* Volume groups
* Volumes
* Protection groups
    * Replication schedule
    * Snapshot schedule

This role was created thinking that every volume need to be part of a volume group because of that to create a volume you just need to:
```
purefa_volumegroups:
- name: test-vg
  volumes:
  - name: test-vol
    size: 5G
  - name: test-vol2
    size: 10G
```

You can specify other parameter for `volume groups` and `volumes`:

| Variable | required | type | default | description |
| -------- | -------- | ---- | ------- | ----------- |
| purefa_volumegroups[].name      | **Y** | str |  | The name of volume group. |
| purefa_volumegroups[].eradicate | N     | bool | false | Define whether to eradicate the volume group on delete and leave in trash. |
| purefa_volumegroups[].state     | N     | str | present | Define whether the volume group should exist or not. |
| purefa_volumegroups[].bw_qos    | N     | str | present | Bandwidth limit for vgroup in M or G units. M will set MB/s. G will set GB/s. To clear an existing QoS setting use 0 (zero) |
| purefa_volumegroups[].iops_qos  | N     | str | present | IOPs limit for vgroup - use value or K or M. K will mean 1000. M will mean 1000000. To clear an existing IOPs setting use 0 (zero) |
| purefa_volumegroups[].volumes   | N     | list |  | list of volumes in that group `{name: str, size: str}` |
| purefa_volumegroups\[].volumes\[].name      | **Y** | str |  | The name of the volume. |
| purefa_volumegroups\[].volumes\[].size      | N     | str |  | Volume size in M, G, T or P units. |
| purefa_volumegroups\[].volumes\[].qos       | N     | str |  | Bandwidth limit for vgroup in M or G units. M will set MB/s. G will set GB/s. To clear an existing QoS setting use 0 (zero) |
| purefa_volumegroups\[].volumes\[].iops_qos  | N     | str |  | IOPs limit for vgroup - use value or K or M. K will mean 1000. M will mean 1000000. To clear an existing IOPs setting use 0 (zero) |
| purefa_volumegroups\[].volumes\[].eradicate | N     | bool | false | Define whether to eradicate the volume on delete or leave in trash. |
| purefa_volumegroups\[].volumes\[].state     | N     | str | present | Define whether the volume should exist or not. |

You can still specify `protection group` setting for each `volume group` for example (to enable replication and snapshot schedule):
```
- name: test-vg
  pg:
    replication:
      enabled: true
    snapshot:
      enabled: true
```

All the options are:


| Variable | required | type | default | description |
| -------- | -------- | ---- | ------- | ----------- |
| purefa_volumegroups[].pg.pgroup    | N | str | volume group name | The name of the protection group. |
| purefa_volumegroups[].pg.target    | N | list | "{{ pgroup_target }}" | List of remote arrays or offload target for replication protection group to connect to. Note that all replicated protection groups are asynchronous. Target arrays or offload targets must already be connected to the source array. Maximum number of targets per Portection Group is 4, assuming your configuration suppors this. |
| purefa_volumegroups[].pg.state     | N     | str | present | Define whether the volume group should exist or not. |
| purefa_volumegroups[].pg.eradicate | N     | bool | false | Define whether to eradicate the protection group on delete and leave in trash. |
| purefa_volumegroups[].pg.enabled   | N     | bool | true | Define whether to enabled snapshots for the protection group. |


### Hosts
You can use the variable `purefa_hostgroups` to manage host groups and hosts:

```yml
purefa_hostgroups:
- name: hostGroup1
  hosts:
  - name: hos1
    iqn: iqn.1993-08.org.debian:01:xxx
  - name: host2
    iqn: iqn.1993-08.org.debian:01:xxx
  connections:
    flasharray01:
    - vg_name1/volume1
    - vg_name1/volume2
```


Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables
passed in as parameters) is always nice for users too:
```yaml
- hosts: servers
  collections:
  - purestorage.flasharray
  roles:
  - name: powerhome.purefa
    vars:
      purefa_url: pure.com
      purefa_api_token: api_token
```

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a
website (HTML is not allowed).
