# hadoop-ansible  
This repository contains Ansible playbooks for deploying Hadoop and HBase  

Using Hadoop playbook you can deploy Hadoop cluster with Namenode, SecondaryNamenode and DataNodes.  
HBase playbook supports deployment with master, backup masters and regionservers.  

Playbooks designed for:  
Hadoop version: 2.6.0  
Hbase version: 1.3.0  

### Deployment Configuration  

`vars/env.yml`  - contains variables that can override default variables for roles

You need to specify private SSH key for hbase and hadoop uses.  
These keys are used for Hadoop and HBase startup scrips  

```
hbase_ssh_private_key: "~/.ssh/hbase_rsa"
hbase_ssh_authorized_keys: "~/.ssh/authorized_keys_hbase"
hadoop_ssh_private_key: "~/.ssh/hadoop_rsa"
hadoop_ssh_authorized_keys: "~/.ssh/authorized_keys_hadoop"
```

Optionally you can specify proxy address that will be used during installation of the packages:  

```
proxy_env:
  http_proxy: "proxy_ip:port"
  htts_proxy: "proxy_ip:port"
```

### Ansible inventory file

You can use `inventory.example` file as a template to create your own Ansible inventory file.

```
[hadoop_namenode]
10.0.0.2

[hadoop_secondarynamenode]
10.0.0.3

[hadoop_datanodes]
10.0.0.3
10.0.0.4

[hadoop_all:children]
hadoop_namenode
hadoop_datanodes

[hbase_master:children]
hadoop_namenode

[hbase_regionservers:children]
hadoop_datanodes

[hbase_backup_masters]
10.0.0.3

[hbase_all:children]
hbase_master
hbase_regionservers

```

### Ansible config file  

You can use `ansible.cfg.example` file as a template to create your own Ansible configuration file.

```
[defaults]
inventory = ./inventory
host_key_checking = False
timeout = 10
remote_user = root
private_key_file = ~/.ssh/id.rsa
nocows = 1
```

### Deployment

To deploy Hadoop and HBase run:

```
ansible-playbook site.yml
```
Also you can run deployment for Hadoop and HBase separately:

```
ansible-playbook deploy-hadoop.yml
ansible-playbook deploy-hbase.yml
```

### Tests

You upload test file to hdfs, you can run next playbook:  

```
ansible-playbook upload-test-file.yml
```
