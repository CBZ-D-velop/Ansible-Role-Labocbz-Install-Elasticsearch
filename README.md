# Ansible role: labocbz.install_elasticsearch

![Licence Status](https://img.shields.io/badge/licence-MIT-brightgreen)
![CI Status](https://img.shields.io/badge/CI-success-brightgreen)
![Testing Method](https://img.shields.io/badge/Testing%20Method-Ansible%20Molecule-blueviolet)
![Testing Driver](https://img.shields.io/badge/Testing%20Driver-docker-blueviolet)
![Language Status](https://img.shields.io/badge/language-Ansible-red)
![Compagny](https://img.shields.io/badge/Compagny-Labo--CBZ-blue)
![Author](https://img.shields.io/badge/Author-Lord%20Robin%20Crombez-blue)

## Description

![Tag: Ansible](https://img.shields.io/badge/Tech-Ansible-orange)
![Tag: Debian](https://img.shields.io/badge/Tech-Debian-orange)
![Tag: SSL/TLS](https://img.shields.io/badge/Tech-SSL%2FTLS-orange)
![Tag: Elasticsearch](https://img.shields.io/badge/Tech-Elasticsearch-orange)
![Tag: Standalone](https://img.shields.io/badge/Tech-Standalone-orange)
![Tag: Cluster](https://img.shields.io/badge/Tech-Cluster-orange)

An Ansible role install and configure Elasticsearch your server.

## Folder structure

By default Ansible will look in each directory within a role for a main.yml file for relevant content (also man.yml and main):

```PYTHON
.
├── README.md  # Contains an overview of the role and its purpose.
├── defaults
│   ├── main.yml  # Contains default variables for the role that can be overridden by users.
│   └── README.md  # Contains documentation for the default variables.
├── files
│   └── README.md  # Contains documentation for the files in the directory.
├── handlers
│   ├── main.yml  # Contains handlers that can be called by tasks within the role.
│   └── README.md  # Contains documentation for the handlers.
├── meta
│   ├── main.yml  # Contains metadata about the role, including dependencies and supported platforms.
│   └── README.md  # Contains documentation for the role metadata.
├── tasks
│   ├── main.yml  # Contains tasks to be executed by the role on the managed nodes.
│   └── README.md  # Contains documentation for the tasks.
├── templates
│   └── README.md  # Contains documentation for the templates.
└── vars
    ├── main.yml  # Contains variables that are specific to the role and are not meant to be overridden.
    └── README.md  # Contains documentation for the role variables.
```

## Tests and simulations

### Basics

You have to run multiples tests. *tests with an # are mandatory*

```MARKDOWN
# lint
# syntax
# converge
# idempotence
# verify
side_effect
```

Executing theses test in this order is called a "scenario" and Molecule can handle them.

Molecule use Ansible and pre configured playbook to create containers, prepare them, converge (run the role) and verify its execution.
You can manage multiples scenario with multiples tests in order to get a 100% code coverage.

This role contains a ./tests folder. In this folder you can use the inventory or the tower folder to create a simualtion of a real inventory and a real AWX / Tower job execution.

### Command reminder

```SHELL
# Check your YAML syntax
yamllint -c ./.yamllint .

# Check your Ansible syntax and code security
ansible-lint --config=./.ansible-lint .

# Execute and test your role
molecule lint
molecule create
molecule list
molecule converge
molecule verify
molecule destroy

# Execute all previous task in one single command
molecule test
```

## Installation

To install this role, just copy/import this role or raw file into your fresh playbook repository or call it with the "include_role/import_role" module.

## Usage

### Vars

Some vars a required to run this role:

```YAML
---
elasticsearch_major_version: "8"

elasticsearch_config_path: "/etc/elasticsearch"
elasticsearch_host: "0.0.0.0"
elasticsearch_port: 9200
elasticsearch_cluster_name: "my.elasticsearch-cluster.tld"
elasticsearch_data_path: "/var/lib/elasticsearch"
elasticsearch_group_name: "ELASTICSEARCH"
elasticsearch_temp_path: "{{ elasticsearch_config_path }}/tmp"

elasticsearch_p12_password: "myPassword"
elasticsearch_client_auth: false
elasticsearch_ssl_authorities: "/etc/ssl/cacert"

elasticsearch_ssl_path: "{{ elasticsearch_config_path }}/ssl"

elasticsearch_elastic_password: "myVeryStringP@ssword"

elasticsearch_ram: "4g"

elasticsearch_group: "elasticsearch"

```

The best way is to modify these vars by copy the ./default/main.yml file into the ./vars and edit with your personnals requirements.

You can set vars in the template model in Ansible AWX / Tower or just surchage them during the playbook call.

In order to surchage vars, you have multiples possibilities but for mains cases you have to put vars in your inventory and/or on your AWX / Tower interface.

```YAML
# From inventory
---
inv_elasticsearch_major_version: "8"

inv_elasticsearch_port: 9200
inv_elasticsearch_cluster_name: "my.elasticsearch-cluster.tld"
inv_elasticsearch_data_path: "/var/lib/elasticsearch"
inv_elasticsearch_config_path: "/etc/elasticsearch"
inv_elasticsearch_group_name: "local"

inv_elasticsearch_p12_password: "secret"
inv_elasticsearch_client_auth: true
inv_elasticsearch_ssl_path: "{{ inv_elasticsearch_config_path }}/ssl"
inv_elasticsearch_ssl_authorities: "{{ inv_elasticsearch_ssl_path }}/My-Local-CA-Authority/My-Local-CA-Authority.crt"

inv_elasticsearch_elastic_password: "myVeryStringP@ssword"

inv_elasticsearch_ram: "1g"

```

```YAML
# From AWX / Tower
---

```

### Run

To run this role, you can copy the molecule/default/converge.yml playbook and add it into your playbook:

```YAML
- name: "Include labocbz.install_elasticsearch"
    tags:
    - "labocbz.install_elasticsearch"
    vars:
    elasticsearch_major_version: "{{ inv_elasticsearch_major_version }}"
    elasticsearch_port: "{{ inv_elasticsearch_port }}"
    elasticsearch_cluster_name: "{{ inv_elasticsearch_cluster_name }}"
    elasticsearch_elastic_password: "{{ inv_elasticsearch_elastic_password }}"
    elasticsearch_ram: "{{ inv_elasticsearch_ram }}"
    elasticsearch_data_path: "{{ inv_elasticsearch_data_path }}"
    elasticsearch_p12_password: "{{ inv_elasticsearch_p12_password }}"
    elasticsearch_ssl_path: "{{ inv_elasticsearch_ssl_path }}"
    elasticsearch_config_path: "{{ inv_elasticsearch_config_path }}"
    elasticsearch_group_name: "{{ inv_elasticsearch_group_name }}"
    elasticsearch_client_auth: "{{ inv_elasticsearch_client_auth }}"
    elasticsearch_ssl_authorities: "{{ inv_elasticsearch_ssl_authorities }}"
    ansible.builtin.include_role:
    name: "labocbz.install_elasticsearch"
```

## Architectural Decisions Records

Here you can put your change to keep a trace of your work and decisions.

### 2023-05-19: First Init

* First init of this role with the bootstrap_role playbook by Lord Robin Crombez
* Fix the tmp dir for java by edit the var (see [here](https://github.com/elastic/elasticsearch/issues/57018))
* Change the elastic user password by reset it and change it by the API (see [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-api-change-password.html))
* Role is Cluster mode friendly, by adding all nodes from group to the cluster (see [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/important-settings.html))
* You can get the cluster and verify by check the cluster health (see [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-health.html))
* All nodes are master-eligible, because you can't bootstrap a cluster with data already present (see [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-bootstrap-cluster.html)), so the role purge all data if any changes are done into the elasticsearch.yml file ! WARNING
* Cluster use custom pem/key files, not a jks or a p12, but p12 and p12 password are required to run the role even if they are NOT used (see [here](https://opster.com/guides/elasticsearch/security/elasticsearch-cluster-security/))
* It seem we can use users in no protected mode (security=false) and you can't use such a conf if no SSL/TLS files ares available (see[here](https://discuss.elastic.co/t/unable-to-authenticate-user-for-rest-request/197461)) but the role will create the cluster
* You can't keep old data from Elasticsearch when you upgrade to a cluster, you have to export your data BEFORE anything (see[here](https://www.elastic.co/guide/en/elasticsearch/reference/current/add-elasticsearch-nodes.html))

### 2023-05-30: Cryptographic update

* SSL/TLS Materials are not handled by the role
* Certs/CA have to be installed previously/after this role use

## Authors

* Lord Robin Crombez

## Sources

* [Ansible role documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
* [Ansible Molecule documentation](https://molecule.readthedocs.io/)
