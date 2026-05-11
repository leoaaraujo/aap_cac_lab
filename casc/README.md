# CASC-DEMO
ansible-navigator run casc_ctrl_config.yml \
  -i inventory \
  -e '{orgs: customera, dir_orgs_vars: orgs_vars}' \
  -m stdout \
  --eei localhost/ee-casc:latest --pull-policy missing

ansible-navigator run casc_ctrl_config.yml \
  -i inventory \
  -e '{orgs: customerb, dir_orgs_vars: orgs_vars}' \
  -m stdout \
  --eei localhost/ee-casc:latest --pull-policy missing
