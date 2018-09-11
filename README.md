# SOC 8 CLM Quick Start Guide

The doku directory contains some information about "how to setup SOC8" for a test in a full virtualized environemnt. This is still WIP - at the moment, I try to deploy SOC8 in one big VM with 128GB RAM - running on Azure....

# CLIM 
```
# clim <OPTIONS> <COMMAND>

wrapper script for using SUSE Cloud 8

This script should help to use the Cloud Lifecycle Manager of SUSE OpenStack Cloud 8. It runs the ansible scripts in the correct directorys. 
HELP:
  -c                                   :run config-processor with option "-e remove_deleted_servers=True -e free_unused_addresses=True"
  -o "<OPTIONS>"                       :add <OPTIONS> to the end of the ansoble-playbook call
  -l "<RESOURCE>"                      :if the service command is used, limit the execution to the resource <RESOURCE>
  -n notify via pushover

COMMANDS
  prepare "MESSAGE"                    : commits the config changes, run the config-processor and run the ready-deployment if the config is valid
  site                                 : deploys the config to the nodes via the site.yml playbook
  status                               : checks the status of the services
  stop                                 : shutdown the openstack services
  start                                : start the openstack services
  service <OPENSTACK-SERVICE> <ACTION> : runs the ansible playbook to execute <ACTION> on the service <OPENSTACK>
                                         e.x.: "clim service neutron restart"
  service list                         : list all services
  setup_bashrc                          : crates a .bashrc file and setup variables which can be used as a shortcut for jumping in the correct directory:
                                          - $def=~/openstack/my_cloud/definition
                                          - $data=~/openstack/my_cloud/definition/data
                                          - $config=~/openstack/my_cloud/config
                                          - $scratch=~/scratch/ansible/next/ardana/ansible
                                            to jump into the DATA directory of the input model, just run "cd $data"


