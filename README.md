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
