# This a collection of Ansible scripts

Ansible 2.1 or higher required!

If /usr/bin/python is not available link it (e.g. in Ubuntu 16.xx)

```
ansible -i myhosts/hosts_3nodes  -m raw -a "ln -s /usr/bin/python3 /usr/bin/python" all -v
```

## Supported OS

* Redhat 7 or higher
* CentOS 7 or higher
* Ubuntu 14.x (not 16.x)
* Suse SLES 12 or higher

## Verify inventory file

Execute:

```
ansible -i myhosts/<yourfile>  -m command -a "hostname -f" all
```

## Use the UI installer

This script sets up the Java, mapr user with password `mapr123`, install ntp and rpcbind. Last step is that it launches the MapR-UI installer on the master-node.
Use `hosts_run-installer-template` as template and copy it.


Execute:

```
ansible-playbook -i hosts_run-installer-template site-run-installer.yml
```

Goto `https://<masternode>:9443` and login with mapr and password mapr123.
Click wizard and install.

## Inventory file templates

Can be found `host_templates`

## Install cluster

Use `host_templates/hosts_cluster` as template and copy it and the hostnames to the components you want to get installed. If components are not required, just leave the block empty. Then run:

```
ansible-playbook -i hosts_template site-cluster.yml
```

The `site-cluster.yml` script always ensures the cluster state. This means you can re-run this script, after you removed or added new components or nodes.
That an unprovisioned node is still known to Ansible you have to add the removed node to [unprovision] block in inventory file.

ATTENTION: Never move critical components in one run, e.g. you have two CLDB installed on node1 and node2 and you want move it to node3 and node4. In that case first install two new CLDBs and in
the next run remove the other ones.

## Install client

Use `host_templates/hosts_client` as template and copy it and the hostnames to the components you want to get installed. If components are not required, just leave the block empty. Then run:

```
ansible-playbook -i hosts_template site-client.yml
```

## Install RStudio Server (m5 license requried for NFS)

Use `hosts_template` as template and copy it and the hostnames to the components you want to get installed. If components are not required, just leave the block empty. Then run:

```
ansible-playbook -i hosts_template rstudio-server.yml
```

## Install Zeppelin

Use `hosts_template` as template and copy it and the hostnames to the components you want to get installed. If components are not required, just leave the block empty. Then run:

```
ansible-playbook -i hosts_template site-zeppelin.yml
```

## Helpers

Helpers are located in the `helper` folder.

### Create several users to test ACE

This document contains the ACE file/Volume demo
To create the users and groups just run:

Run:

```
ansible-playbook -i hosts_template helper/create-user-ace.yml
```

### Setup Kerberos, SSSD and PAM with ActiveDirectory (Only tested on Redhat 7.3!!!)

Update values in `helper/group/vars`
Use `myhosts/hosts_kerberos as template

```
ansible-playbook -i myhosts/hosts_kerberos helper/kerberos-sssd-setup.yml
```

Afterwards authenticate with users from AD.

### Create Kerberos users in Active Directory for MapR

Based on number of hosts users are created in AD.

```
ansible-playbook -i myhosts/hosts_kerberos helper/kerberos-createadusers.yml
```

### Generate keytabs based on created users in command above.

Keytabs are stored in folder defined in mapr_kerberos_local_keytabs_dir on ansible client machine

```
ansible-playbook -i myhosts/hosts_kerberos helper/kerberos-keytabs-ad-generate.yml 
```


### Verify keytabs which are the base for MapR installation

Keytabs are stored in folder defined in mapr_kerberos_local_keytabs_dir on ansible client machine.
When the customer delivers keytabs this can be also used to validate.

```
ansible-playbook -i myhosts/hosts_kerberos helper/kerberos-keytabs-verify.yml      
```