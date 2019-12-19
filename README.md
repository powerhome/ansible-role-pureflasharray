Purefa
=========

Configure PURE FlashArray.

To download the role you can use `ansible-galaxy`. For that create a file `requirements.yml` with the following content:

```yml
---
# purefa role
- src: https://github.com/powerhome/ansible-purefa
  version: master
  name: powerhome.purefa

```

Afte that run the command:
```
ansible-galaxy role install -r requirements.yml -p <ansible_roles_directory_path>
```

PS: This role isn't tested with molecule since there isn't a way to provision a test environment. Molecule was used only to create the directory hierarchy.

Requirements
------------

* `python 3`
* `purestorage 1.18.1`
```
pip install purestorage
```

Role Variables
--------------

A description of the settable variables for this role should go here, including
any variables that are in defaults/main.yml, vars/main.yml, and any variables
that can/should be set via parameters to the role. Any variables that are read
from other roles and/or the global scope (ie. hostvars, group vars, etc.) should
be mentioned here as well.

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables
passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: powerhome.purefa, purefa_url: pure.com, purefa_api_token: api_token }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a
website (HTML is not allowed).
