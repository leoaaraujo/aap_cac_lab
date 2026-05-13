# CASC AAP Demo


- To apply the configuration as code

```shell

# Organization: Customer A
ansible-navigator run casc_ctrl_config.yml \
  -i inventory \
  -e '{orgs: customera, dir_orgs_vars: orgs_vars}' \
  -m stdout \
  --eei localhost/ee-casc:latest --pull-policy missing

# Organization: Customer B
ansible-navigator run casc_ctrl_config.yml \
  -i inventory \
  -e '{orgs: customerb, dir_orgs_vars: orgs_vars}' \
  -m stdout \
  --eei localhost/ee-casc:latest --pull-policy missing
```


<br>

- To export objects from AAP

```shell

# Organization: Customer A
ansible-navigator run casc_ctrl_export.yml \
-i inventory \
-e '{"orgs":"Customer A", "output_path":"/path/to/backup", "input_tags": ["controller_credentials","controller_projects","controller_projects"]}' \
-m stdout \ 
--eei localhost/ee-casc:latest --pull-policy missing


# Organization: Customer B
ansible-navigator run casc_ctrl_export.yml \
-i inventory \
-e '{"orgs":"Customer B", "output_path":"/path/to/backup", "input_tags": ["controller_credentials","controller_projects"]}' \
-m stdout \ 
--eei localhost/ee-casc:latest --pull-policy missing

```
