# TODOs

* Firewall stuff!
* Deploy Ansible user and SSH key (see profile_openshift3::ansible)
* Default ansible_hosts_vars and merge with the one from Hiera -> belongs into profile?!
* ansible_hosts_vars probably in init.pp for further processing
* Manage /etc/sysconfig/docker-storage-setup
* Manage partitioning (LVM volume preparation)
* Integrate Gluster Playbook ?
* Registry IP
* Yum Versionlock support
* Support for OCP
* Integration into profile_openshift3
* Documentation
** vagrant.dev is not allowed in resolv.conf search when landrush returns wildcard
   answers. see
* openshift_hosted_router_selector='region=infra'
* openshift_hosted_router_replicas=2
* openshift_hosted_registry_selector='region=infra'
* openshift_hosted_registry_replicas=2
* https://github.com/openshift/openshift-ansible/blob/master/inventory/byo/hosts.origin.example

# Example Hieradata

## vagrant/hieradata_nodes/origin-master1.vagrant.dev.yaml

```
---
classes:
  - openshift::role::ansible_master
  - profile_openshift3::ansible

openshift::role::ansible_master::run_ansible: false
openshift::role::ansible_master::masters_as_nodes: true
openshift::role::ansible_master::playbooks_version: 'openshift-ansible-3.3.28-1'
openshift::role::ansible_master::ansible_hosts_vars:
  ansible_become: true
  ansible_ssh_user: ansible
  containerized: true
  deployment_type: origin
  docker_version: 1.10.3
  openshift_hosted_manage_registry: true
  openshift_install_examples: true
  openshift_master_default_subdomain: apps.test.example.com
  openshift_master_identity_providers: [{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider', 'filename': '/etc/origin/master/htpasswd'}]
  openshift_release: v1.3.0
  openshift_use_manageiq: false
  openshift_use_nuage: false
  os_sdn_network_plugin_name: redhat/openshift-ovs-multitenant
openshift::role::ansible_master::ansible_hosts_children:
  masters:
    - name: origin-master1.vagrant.dev
      ip: 192.168.216.201
      node_labels:
        region: infra
      schedulable: true
  nodes:
    - name: origin-node1.vagrant.dev
      ip: 192.168.216.210
      node_labels:
        region: vagrant
    - name: origin-node2.vagrant.dev
      ip: 192.168.216.211
      node_labels:
        region: vagrant

profile_openshift3::ansible::ssh_public_key: ''
profile_openshift3::ansible::ssh_private_key: |
```

## vagrant/hieradata_nodes/origin-node1.vagrant.dev.yaml

```
---
classes:
  - profile_openshift3::ansible

profile_openshift3::ansible::ssh_public_key: ''
```

## vagrant/hieradata_nodes/origin-node2.vagrant.dev.yaml

```
---
classes:
  - profile_openshift3::ansible

profile_openshift3::ansible::ssh_public_key: ''
```

# vmdefinitions

## vagrant/vmdefinitions/origin-master1.vagrant.dev.yaml

```
---
## Puppet ENC
environment: 'VshnProduction'
parameters:
  customer: 'local'
  project: 'origin'
  role: 'master'
  location: 'vagrant'
  stage: 'dev'

## Vagrant VM definition
cores: 2
memory: 2048
box: 'puppetlabs/centos-7.2-64-nocm'
private_networks:
  default:
    type: 'static'
    ip: '192.168.216.201'
    auto_config: true
```

## vagrant/vmdefinitions/origin-node1.vagrant.dev.yaml

```
---
## Puppet ENC
environment: 'VshnProduction'
parameters:
  customer: 'local'
  project: 'origin'
  role: 'master'
  location: 'vagrant'
  stage: 'dev'

## Vagrant VM definition
cores: 2
memory: 2048
box: 'puppetlabs/centos-7.2-64-nocm'
private_networks:
  default:
    type: 'static'
    ip: '192.168.216.210'
    auto_config: true
```

## vagrant/vmdefinitions/origin-node2.vagrant.dev.yaml

```
---
## Puppet ENC
environment: 'VshnProduction'
parameters:
  customer: 'local'
  project: 'origin'
  role: 'master'
  location: 'vagrant'
  stage: 'dev'

## Vagrant VM definition
cores: 2
memory: 2048
box: 'puppetlabs/centos-7.2-64-nocm'
private_networks:
  default:
    type: 'static'
    ip: '192.168.216.211'
    auto_config: true
```

