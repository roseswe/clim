# clim
wrapper script for using SUSE Cloud 8

This script should help to use the Cloud Lifecycle Manager of SUSE OpenStack Cloud 8. It runs the ansible scripts in the correct directorys. 

HELP:
clim -cn <COMMAND> <PARAMETER>

OPTIONS
  -c run config-processor with option "-e remove_deleted_servers=True -e free_unused_addresses=True"
  -n notify via pushover

COMMANDS
  prepare "MESSAGE": commits the config changes, run the config-processor and run the ready-deployment if the config is valid
  site             : deploys the config to the nodes via the site.yml playbook
  status	   : checks the status of the services
  stop		   : shutdown the openstack services
  start		   : start the openstack services
