Ironic Inspector Rules
======================

This role provides a module, `ironic_inspector_rules`, which may be
used to configure introspection rules in OpenStack ironic inspector.
It installs required python dependencies in a virtualenv, and uses
the `ironic_inspector_rules` module to configure a set of rules.

Requirements
------------

The OpenStack ironic inspector API should be accessible from the
target host.

Role Variables
--------------

`ironic_inspector_venv` is a path to a directory in which to create a
virtualenv.

`ironic_inspector_auth_type` is an authentication type compatible with
the `auth_type` argument of `os_*` Ansible modules.

`ironic_inspector_auth` is a dict containing authentication information
compatible with the `auth` argument of `os_*` Ansible modules.

`ironic_inspector_url` is the URL of Ironic Inspector API endpoint,
required if no authentication is used.

`ironic_inspector_rules` is a list of introspection rules which should
exist. See the Inspector rules API for details of parameters available
for rules.

Dependencies
------------

This role depends on the Kayobe `shade` role.

Example Playbook
----------------

The following playbook configures an introspection rule to set the IPMI
username and password fields in a node's driver info if they are currently
empty.

    ---
    - name: Ensure ironic inspector introspection rules are configured
      hosts: ironic-inspector
      roles:
        - role: ironic-inspector-rules
          ironic_inspector_venv: "~/ironic-inspector-rules-venv"
          ironic_inspector_auth_type: "password"
          ironic_inspector_auth:
            project_name: <keystone project>
            username: <keystone user>
            password: <keystone password>
            auth_url: <keystone auth URL>
          ironic_inspector_rules:
            - description: "Set IPMI driver_info if no credentials"
              conditions:
                - field: "node://driver_info.ipmi_username"
                  op: "is-empty"
                - field: "node://driver_info.ipmi_password"
                  op: "is-empty"
              actions:
                - action: "set-attribute"
                  path: "driver_info/ipmi_username"
                  value: "<IPMI username>"
                - action: "set-attribute"
                  path: "driver_info/ipmi_password"
                  value: "<IPMI password>"

Author Information
------------------

- Mark Goddard (<mark@stackhpc.com>)