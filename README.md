# ansible_lab
Ansible lab environment as part of the [ipspace.net](https://www.ipspace.net) [Network automation course](https://www.ipspace.net/Building_Network_Automation_Solutions).

# Table of contents
1. [Environment description](#environment_description)
2. [Topology description](#topology_description)
3. [Ansible configuration](#ansible_configuration)
4. [Implemented playbooks](#implemented_playbooks)


Environment description  <a name="environment_description"></a>
-----------------------
The lab is built in a VMware 6.5 ESXi host.
It consists of two virtual servers:

**Eve-NG:**
This is a network virtualisation platform which an interface to building virtual network topologies of many vendors of networking equipment. [Eve-NG](http://eve-ng.net/)

**Linux server:**
This server hosts the ansible lab environment. It is running Ubuntu 18.04 LTS, and Ansible 2.7 is installed. 

Both servers have two NICs, which are both are connected through a separate standard vswitch.<br>
The first NIC has a connection to the corporate network, and provides connectivity to the internet using NAT provided by the corporate firewall infrastructure.<br>
The second NIC is connected to a vswitch which only connects the Linux server with the Eve-NG server to allow access to the switches running on Eve-NG.<br>

Local vswitch topology:<br>
<img src='https://github.com/erikruiter2/ansible_lab/raw/master/doc/vswitch_local.png' width=400>

Uplink vswitch topology:<br>
<img src='https://github.com/erikruiter2/ansible_lab/raw/master/doc/vswitch_uplink.png' width=400>

Topology description  <a name="topology_description"></a>
--------------------
On Eve-NG there runs a virtual network environment with the following switches:

| hostname        | IP           | Description  |
| :------------ |:-------------| :-----|
| s11-iol     | 10.100.1.11 | Cisco IOL virtual switch |
| s12-iol     | 10.100.1.12 | Cisco IOL virtual switch |
| s13-iol     | 10.100.1.13 | Cisco IOL virtual switch |
| s14-iol     | 10.100.1.14 | Cisco IOL virtual switch |
| s15-iol     | 10.100.1.15 | Cisco IOL virtual switch |
| mgmt-iol     | 10.100.1.100 | Cisco IOL virtual switch used for aggregation and uplink to ansible host|
| erikbuntu    | 10.100.1.20     | Ansible host|

All switches have a interface in a switch management vlan, which is switched through the  management switch (mgmt-iol) to the outside, towards the Ansible host.

<img src='https://github.com/erikruiter2/ansible_lab/raw/master/doc/eve-topo.png' width=500>



Ansible configuration  <a name="ansible_configuration"></a>
----------------------
At the start of the course, the Ansible configuration is still basic.<br>
Here is an overview of the files currently in use:

| File | Description |
| :---- | :----- |
| ansible.cfg  | Standard ansible configuration file, with tweaks for vault and inventory location |
| inventory | Basic static inventory file listing all switches in the lab environment|
| group_vars/lab/vars | placeholder for global variables related to the lab group|
| group_vars/lab/vault| placeholder for vault variables related to the lab group (currently contains ssh password for Cisco switches)|

Example of raw adhoc command:
```
erikr@erikbuntu:~/ansible_lab$ ansible -m raw -a "show run | i hostname" lab
s12-iol | CHANGED | rc=0 >>
hostname s12-iol
Shared connection to s12-iol closed.


s14-iol | CHANGED | rc=0 >>
hostname s14-iol
Shared connection to s14-iol closed.


s11-iol | CHANGED | rc=0 >>
hostname s11-iol
Shared connection to s11-iol closed.


s13-iol | CHANGED | rc=0 >>
hostname s13-iol
Shared connection to s13-iol closed.


s15-iol | CHANGED | rc=0 >>
hostname s15-iol
Shared connection to s15-iol closed.
```


Implemented playbooks <a name="implemented_playbooks"></a> 
---------------------

**[backup_config.yml:](../../blob/master/backup_config.yml)**
Playbook which reads config from all hosts and stores it in the device_config directory. Changes are also commited to the repository

**[napalm_get_facts.yml:](../../blob/master/napalm_get_facts.yml)**
Basic playbook to get napalm facts from devices. A filter can be specified when running the playbook.

**[cisco_lldp_topo.yml:](../../blob/master/cisco_lldp_topo.yml)** (2nd course assignment)
Playbook wheach reads config from all hosts and tries to build an LLDP topology graph using graphviz 

**[cisco_provisioning.yml:](../../blob/master/cisco_provisioning.yml)** (3rd/4th course assignment)
Playbook which generates and installs basic cisco device config.

**[cisco_vlan_service.yml:](../../blob/master/cisco_vlan_service.yml)** (3rd/4th course assignment)
Playbook which generates and installs cisco config for provisioning a layer 2 vlan service per port.

**[cisco_validate.yml:](../../blob/master/cisco_validate.yml)** (3rd/4th course assignment)
Playbook which does a config check to see if the live config is as intended.

* More to be added as the course progresses
