End to End LAB Deployment with ACI-CNI
======
## Requirements:
### Ansible Host:
* Install latest ansible version (I tested this with 2.6) 
  * http://docs.ansible.com/ansible/latest/intro_installation.html
  * Install all ansible requirements 
* Python 2.7.9 or higher (I tested with 2.7.12)
* Install PIP
  * curl https://bootstrap.pypa.io/get-pip.py | python
* Install pyvmom (I tested pyvmomi 6.5.0.2017.5)
  * pip install pyvmomi
* Install python-netaddr
  * pip install netaddr
* If using password to authenticate ssh session, you need to install sshpass [Installation guide](https://gist.github.com/arunoda/7790979)
* Disable host key checking:  edit the file `/etc/ansible/ansible.cfg` and set the `host_key_checking = False`
### Supported OS:
* Ubuntu 16.04

* If you want you can grab an Ubuntu VM at this link: [Ubuntu-16.04](https://cisco.box.com/s/hiu0chr0f5el6k9vlt1du912ybn7okuj)
  * This VM is configured with:
    * 2 CPU, 16GB HD, 2GB RAM and 2 NIC
    * username/pass: cisco/123Cisco123
    * The VM IS NOT a Template. It is a normal VM with a Snapshot called "Base".
    * By default the scripts deployes Linked Clones, if you are ok with this no action is needed. 

* New Virtual Machine requirements:
  * Two NICs
  * Install SSH and Python
  * PowerOff the VM, Createa a Snapshot.
    * If you call the Snapsop anything else than "Base" edit `vm_snapshotnam`e variale in `inventory/group_vars/all.yml`

* All the VM parametes, hostname, IP etc... are taken from the inventory file and the `inventory/group_vars/all.yml` file.
### Supported K8S Versions:
* 1.10 to 1.6, I tested with 1.10 only

## ACI Fabric Pre-Requisites
Your fabric needs to have basic connectivity pre-configured for your hosts. 
This scripts assumes you are deploying VMs as such I expect to have ACI configured with VMM integration to your vCenter. 

### Tenant(s), VRF and L3OUT
For the tenant configuration you have two options:
* Configure your kubernetes VRF and L3OUT in the common tenant and have a separate tenant for the Kubernetes cluster (Preferred Option)
* Configure everything in a dedicated Kubernetes tenant

I would recommend to use the first option as, un-provisioning a cluster, deletes the Kubernetes Tenant and if you are redeploying it multiple time you will need to re-configure every time the VRF and L3OUT. 
The demo configuration that is in this repository is assuming you have deployed the VRF and the L3OUT in common.

## Configure Host Note:


* The hosts are configured with 2 interfaces:
  * ACI facing interface (ens192): This is the interface toward the ACI fabric and is the default GW of the nodes. You MUST have internet connectivity trough this interfaces.
  * Management interface (ens160): I expect the ansible host to be in the same subent, no you can't use this as your default GW


## Download acc-provision:
From Cisco.com download `dist-debs-<version>.tar.gz`, unzip it and place the .deb file under `roles/aci-host/files/`
Thi scrips is pre-configured to use `acc -provision_1.9.0-23_amd64.deb`, if you use a diffenret file you need edit the `inventory/group_vars/all.yml` file.

## Set the parameters 
### inventory/group_vars/all.yml
Customize `inventory/group_vars/all.yml` as per your requirements. 
The included file has the configuration of my lab and every option should be self explanatory or has a comment. 

### inventory/inventory

Configure your inventory file as per your requirements 

# Deploy with this command:
`ansible-playbook -i inventory/inventory -b lab_setup.yml`

