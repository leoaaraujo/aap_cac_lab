# CASC AAP Demo

<br><br>

### Execution Environment Build


- Create an ansible.cfg
```shell
$ cd build-ee

cat << 'EOF' > ansible.cfg
[galaxy]
server_list  =  community_repo,rh-certified_repo,rh-validated_repo
ignore_certs = no

# RH Certified Repo
[galaxy_server.rh-certified_repo]
auth_url = https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
url = https://console.redhat.com/api/automation-hub/content/published/
token = "ADD_YOUR_AUTOMATION_HUB_TOKEN_HERE"

# RH Validated Repo
[galaxy_server.rh-validated_repo]
auth_url = https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
url = https://console.redhat.com/api/automation-hub/content/validated/
token = "ADD_YOUR_AUTOMATION_HUB_TOKEN_HERE"
EOF
```


- Registry Login
```shell
$ podman login registry.redhat.io
```

- Build
```shell
$ ansible-builder build -f execution-environment.yml -t ee-cac:latest --verbosity 3
```

<br><br>

### CaC

- Organization folder structure
```
cac/orgs_vars
├── customera             <---- Folder with the name of the organization to be created    
│   ├── aap_organizations.d
│   │   └── aap_organizations.yml
│   ├── aap_teams.d
│   │   └── aap_teams.yml
│   ├── controller_credentials.d
│   │   └── controller_credentials.yml
│   ├── controller_hosts.d
│   │   └── controller_hosts.yml
│   ├── controller_inventories.d
│   │   └── controller_inventories.yml
│   ├── controller_job_templates.d
│   │   └── controller_job_templates.yml
│   └── controller_projects.d
│       └── controller_projects.yml
└── customerb             <---- Folder with the name of the organization to be created    
    ├── aap_organizations.d
    │   └── aap_organizations.yml
    ├── aap_teams.d
    │   └── aap_teams.yml
    ├── controller_credentials.d
    │   └── controller_credentials.yml
    ├── controller_hosts.d
    │   └── controller_hosts.yml
    ├── controller_inventories.d
    │   └── controller_inventories.yml
    ├── controller_job_templates.d
    │   └── controller_job_templates.yml
    └── controller_projects.d
        └── controller_projects.yml
```

- Create a connection file
```shell
$ cat << 'EOF' > cac/group_vars/all/configure_connection_controller_credentials.yml
---
vault_aap_username: ''
vault_aap_password: ''
vault_aap_hostname: "{{ groups['cac'][0] }}"
vault_aap_validate_certs: 'False'
EOF
```

- Vault Encrypt for the connection file
```shell
$ ansible-vault encrypt cac/group_vars/all/configure_connection_controller_credentials.yml
```

- Vault Encrypt for the credential file
```shell
$ ansible-vault encrypt cac/orgs_vars/ORGANIZATION_NAME/controller_credentials.d/controller_credentials.yml
```

- Create a vault_password
```shell
$ echo "YOUR_VAULT_PASSWORD_HERE" > ~/.vault.password
```

- To apply the configuration as code
```shell
# Organization: Customer A
ansible-navigator run cac_ctrl_config.yml \
  -i inventory \
  -e '{orgs: customera, dir_orgs_vars: orgs_vars}' \
  -m stdout \
  --eei localhost/ee-casc:latest --pull-policy missing --vault-password-file ~/.vault_password

# Organization: Customer B
ansible-navigator run cac_ctrl_config.yml \
  -i inventory \
  -e '{orgs: customerb, dir_orgs_vars: orgs_vars}' \
  -m stdout \
  --eei localhost/ee-casc:latest --pull-policy missing --vault-password-file ~/.vault_password
```

<br><br>

### Export configuration
- To export objects from AAP

```shell

# Organization: Customer A
ansible-navigator run cac_ctrl_export.yml \
-i inventory \
-e '{"organization_filter":"Customer A", "output_path":"/path/to/backup", "input_tag": ["controller_credentials","controller_projects"]}' \
-m stdout \ 
--eei localhost/ee-casc:latest --pull-policy missing


# Organization: Customer B
ansible-navigator run cac_ctrl_export.yml \
-i inventory \
-e '{"organization_filter":"Customer B", "output_path":"/path/to/backup", "input_tag": ["controller_credentials","controller_projects"]}' \
-m stdout \ 
--eei localhost/ee-casc:latest --pull-policy missing

```
