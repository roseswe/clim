# SOC 8 CLM Quick Start Guide

The doku directory contains some information about "how to setup SOC8" for a test in a full virtualized environemnt. This is still WIP - at the moment, I try to deploy SOC8 in one big VM with 128GB RAM - running on Azure....

# CLIM 
clim is a wrapper for the asible based cloud lifecycle manager used in SUSE OpenStack Cloud 8. It will call the ansible playbooks in the correct directory and combines some steps to simplify the installation. It starts long running tasks inside of a screen session - which will not break a job due to a session disconnect. There is also an option to sent a pushover notification to a mobile, after the taks is done (www.pushover.net).

## example tasks:

### deploy a new input model:
The physical nodes are already setuped and the input model is modified. Now you can run:
```
  clim prepare "new config"
  ```
to commit the changes to git, run the config processor and the ready-deployment playbooks. 

To deploy this to the nodes, you can run
```
  clim -n site
```
This will start the site.yml ansible playbook and will notify you on your mobile after it has finished. The playbook itself is running inside of a screen session, so that a disconnect of you ssh session (due to the long runtime) will not break the task.

### deploy a change in your input model

After editing the input model, just run the
```
clim prepare "message"
```
command to commit the changes and prepare the deployer.
If you have done a small change in your input mode, you don't need to run the whole site.yml playbook. Only the service, which is impacted needs a reconfigure. You can use the 
```
  clim service reconfigure <SERVICE>
```
command to start the corresponding playbook

### get the NIC mappings for a new node
After installing a new node (via cobbler), you need to compare the network interfaces with your exsting nodes. If the new server will not fit to the already configured nic_mappings, you have to add a new one. Run the
```
clim show_nic_mapping <IP>
```
to view the nic mappings of the new node. The output is already in yml format - so you can easily copy and past it to the nic_mappings.yml file. The command will connect via ssh to the node and gather the intefaces from the "/sys/proc/net" directory. This will not work in any environment - its tested on kvm only.

## Notify via pushover.net
pushover.net offers the option to sent push messages to your mobile. You need to have a valid account and create a application_token to enable this feature in clim. Put the user data in a ".clim" file in HOME directory of the ardana user. See my example:
```
cat ~.clim
export push_token="<application token>"
export push_user="<user_token>"
export push_msg="Clim run finished"  # default message
export push_device="galaxy"
export push_title="OpenStack Installer CLIM"
export push_URL="https://api.pushover.net/1/messages.json"
```

# clim help:
```
# clim <OPTIONS> <COMMAND>


HELP:
  -c                                   :run config-processor with option "-e remove_deleted_servers=True -e free_unused_addresses=True"
  -o "<OPTIONS>"                       :add <OPTIONS> to the end of the ansible-playbook call
  -l "<RESOURCE>"                      :if the service command is used, limit the execution to the resource <RESOURCE> (= --limit <RESOURCE>
  -n notify via pushover	       : Pushover is a service to push notifications to a mobile (other clients are possible). The credentials for the service
                                         have to be configured in ~/.clim  

COMMANDS
  prepare "MESSAGE"                    : commits the config changes, run the config-processor and run the ready-deployment if the config is valid
  site                                 : deploys the config to the nodes via the site.yml playbook. It will stop if there are uncommited changes in the input model
  status	                       : checks the status of the services. Use "-l <RESOURCE>" to get the status only for <RESOURCE>
  stop		                       : shutdown the openstack services. Use "-l <RESOURCE>" to stop the service only for <RESOURCE>
  start		                       : start the openstack services. Use "-l <RESOURCE>" to start the services only for <RESOURCE>
  reboot <RESOURCE>		       : reboot the node <RESOURCE> gracefully (stop the services, reboot the node and start the services again)

  service <OPENSTACK-SERVICE> <ACTION> : runs the ansible playbook to execute <ACTION> on the service <OPENSTACK>
                                         e.x.: "clim service neutron restart". Use "-l <RESOURCE>" to start the acttion only on <RESOURCE>
  service list                         : list all services
  cobbler-deploy		       : executes the cobbler-deploy playbook
  prepare-sles-grub2i <NODES>          : prepare the pxe install environemnet for <NODE> - executes the prepare-sles-grub2 runbook for <RESOURCE>
  bm-reimage <NODES>                   : reinstall the node <NODE> (or <NODE1,NODE2,NODE3) via cobbler
  bm-power-status <NODES>	       : show the power status of <NODE>
  wipe-disks <NODES>                   : Wipe the disks of <NODES>
  
  update-node <RECOURCE>	       : updates a node: apply all patches, update ardana and show the update-status of the node. 
                                         Patches which need a reboot are included, but the node will not rebooted automatically.

  show_nic_mapping <IP>		       : displays the NIC mappings of the server in a template format, which can be used to add a new mappings to  nic_mappings.yml
  
  setup_bashrc	                       : crates a .bashrc file and setup variables which can be used as a shortcut for jumping in the correct directory:
                                          - $def=~/openstack/my_cloud/definition
                                          - $data=~/openstack/my_cloud/definition/data
                                          - $scratch=~/scratch/ansible/next/ardana/ansible
					  - $config=~/openstack/my_cloud/config
                                            to jump into the DATA directory of the input model, just run "cd $data"

