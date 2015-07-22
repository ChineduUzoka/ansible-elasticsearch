# Ansible Playbook for Elasticsearch
This is an [Ansible](http://www.ansibleworks.com/) playbook for [Elasticsearch](http://www.elasticsearch.org/). You can use it by itself or as part of a larger playbook customized for your local environment.

## Features
- Support for installing plugins

## Testing locally with Vagrant
A sample [Vagrant](http://www.vagrantup.com/) configuration is provided to help with local testing. After installing Vagrant, run `vagrant up` at the root of the project to get an VM instance bootstrapped and configured with a running instance of Elasticsearch. Look at `vars/vagrant.yml` and `defaults/main.yml` for the variables that will be substituted in `templates/elasticsearch.yml.j2`.

## Running Standalone Playbook
### Copy Example Files
Make copies of the following files and rename them to suit your environment. E.g.:

- vagrant-main.yml => my-playbook-main.yml
- vagrant-inventory.ini => my-inventory.ini
- vars/vagrant.yml => vars/my-vars.yml

Edit the copied files to suit your environment and needs. See examples below.

### Edit your my-inventory.ini
Edit your my-inventory.ini and customize your cluster and node names:

```
#########################
# Elasticsearch Cluster #
#########################
[es_node_1]
1.2.3.4.compute-1.amazonaws.com
[es_node_1:vars]
elasticsearch_node_name=elasticsearch-1

[es_node_2]
5.6.7.8.compute-1.amazonaws.com
[es_node_2:vars]
elasticsearch_node_name=elasticsearch-2

[es_node_3]
9.10.11.12.compute-1.amazonaws.com
[es_node_3:vars]
elasticsearch_node_name=elasticsearch-3

[all_nodes:children]
es_node_1
es_node_2
es_node_3

[all_nodes:vars]
elasticsearch_cluster_name=my.elasticsearch.cluster
elasticsearch_plugin_aws_ec2_groups=MyElasticSearchGroup
spm_client_token=<your SPM token here>
```

### Edit your vars/my-vars.yml
See `vars/sample.yml` and `vars/vagrant.yml` for exmaple variable files. These are the files where you specify Elasticsearch settings and apply certain features such as plugins, custom JARs or monitoring. The best way to enable configurations is to look at `templates/elasticsearch.yml.j2` and see which variables you want to defile in your `vars/my-vars.yml`. See below for configurations regarding EC2, plugins and custom JARs.

### Edit your my-playbook-main.yml
Example `my-playbook-main.yml`:

```
---

#########################
# Elasticsearch install #
#########################

- hosts: all_nodes
  user: $user
  sudo: yes

  vars_files:
    - defaults/main.yml
    - vars/my-vars.yml

  tasks:
    - include: tasks/main.yml
```

### Launch
```
$  ansible-playbook my-playbook-main.yml -i my-inventory.ini -e user=<your sudo user for the elasticsearch installation>
```

## Disable Java installation

If you prefer to skip the built-in installation of the Oracle JRE, use the `elasticsearch_install_java` flag:

```
elasticsearch_install_java: "false"
```

## Include role in a larger playbook
### Add this role as a git submodule
Assuming your playbook structure is such as:
```
- my-master-playbook
  |- vars
  |- roles
  |- my-master-playbook-main.yml
  \- my-master-inventory.ini
```

Checkout this project as a submodule under roles:

```
$  cd roles
$  git submodule add git://github.com/traackr/ansible-elasticsearch.git ./ansible-elasticsearch
$  git submodule update --init
$  git commit ./submodule -m "Added submodule as ./subm"
```

### Include this playbook as a role in your master playbook
Example `my-master-playbook-main.yml`:

```
---

#########################
# Elasticsearch install #
#########################

- hosts: all_nodes
  user: ubuntu
  sudo: yes

  roles:
    - ansible-elasticsearch

  vars_files:
    - vars/my-vars.yml
```

# Issues, requests, contributions
This software is provided as is. Having said that, if you see an issue, feel free to log a ticket. We'll do our best to address it. Same if you want to see a certain feature supported in the fututre. No guarantees are made that any requested feature will be implemented. If you'd like to contribute, feel free to clone and submit a pull request.

# Dependencies
None

# License
MIT

# Author Information

George Stathis - gstathis [at] traackr.com
