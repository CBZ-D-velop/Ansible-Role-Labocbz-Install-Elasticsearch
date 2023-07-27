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

This role is designed to install Elasticsearch, both in standalone mode and in a clustered configuration. It offers a comprehensive set of options to tailor Elasticsearch settings based on specific requirements.

Administrators can specify the major version of Elasticsearch to be installed. The role allows setting up Elasticsearch on the desired host and port, along with configuring the cluster name and data path.

SSL/TLS encryption is supported, allowing administrators to secure Elasticsearch communication. The role generates SSL certificates and keys, and administrators can enable client-side authentication if needed.

The role also provides the ability to configure Elasticsearch's JVM memory usage, ensuring efficient utilization of system resources.

For cluster setups, administrators can specify additional hosts to join an existing Elasticsearch cluster. However, it's important to note that data from a standalone setup cannot be directly migrated to a cluster using this role. A fresh installation of Elasticsearch is required when transitioning from standalone to clustered mode.

Furthermore, the role includes a security enhancement feature, allowing administrators to change the password for the Elasticsearch user "elastic" to strengthen the security of the Elasticsearch instance.

Overall, this role simplifies the installation and configuration of Elasticsearch, offering flexibility and customization options to cater to different deployment scenarios, whether standalone or clustered, with or without SSL/TLS encryption.

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
install_elasticsearch_major_version: "8"

install_elasticsearch_config_path: "/etc/elasticsearch"
install_elasticsearch_host: "0.0.0.0"
install_elasticsearch_port: 9200
install_elasticsearch_cluster_name: "my.elasticsearch-cluster.tld"
install_elasticsearch_data_path: "/var/lib/elasticsearch"
install_elasticsearch_temp_path: "{{ install_elasticsearch_config_path }}/tmp"

install_elasticsearch_ssl_path: "{{ install_elasticsearch_config_path }}/ssl"
install_elasticsearch_client_auth: false
install_elasticsearch_elastic_password: "myVeryStringP@ssword"
install_elasticsearch_ssl: true
install_elasticsearch_ssl_authorities: "/etc/ssl/cacert"
install_elasticsearch_ssl_key: "{{ install_elasticsearch_ssl_path }}/{{ install_elasticsearch_cluster_name }}/{{ install_elasticsearch_cluster_name }}.key"
install_elasticsearch_ssl_crt: "{{ install_elasticsearch_ssl_path }}/{{ install_elasticsearch_cluster_name }}/{{ install_elasticsearch_cluster_name }}.crt"

install_elasticsearch_heap: "4g"

install_elasticsearch_group: "elasticsearch"

```

The best way is to modify these vars by copy the ./default/main.yml file into the ./vars and edit with your personnals requirements.

You can set vars in the template model in Ansible AWX / Tower or just surchage them during the playbook call.

In order to surchage vars, you have multiples possibilities but for mains cases you have to put vars in your inventory and/or on your AWX / Tower interface.

```YAML
# From inventory
---
inv_install_elasticsearch_major_version: "8"

inv_install_elasticsearch_port: 9200
inv_install_elasticsearch_cluster_name: "my-elasticcsearch-cluster-role.domain.tld"
inv_install_elasticsearch_data_path: "/var/lib/elasticsearch"
inv_install_elasticsearch_config_path: "/etc/elasticsearch"

inv_install_elasticsearch_client_auth: true
inv_install_elasticsearch_ssl_path: "{{ inv_install_elasticsearch_config_path }}/ssl"
inv_install_elasticsearch_ssl_authorities: "{{ inv_install_elasticsearch_ssl_path }}/my-elasticcsearch-cluster-role.domain.tld/ca-chain.pem.crt"

inv_install_elasticsearch_ssl_key: "{{ inv_install_elasticsearch_ssl_path }}/{{ inv_install_elasticsearch_cluster_name }}/{{ inv_install_elasticsearch_cluster_name }}.pem.key"
inv_install_elasticsearch_ssl_crt: "{{ inv_install_elasticsearch_ssl_path }}/{{ inv_install_elasticsearch_cluster_name }}/{{ inv_install_elasticsearch_cluster_name }}.pem.crt"


inv_install_elasticsearch_elastic_password: "myVeryStringP@ssword"
inv_install_elasticsearch_ssl: true

inv_install_elasticsearch_heap: "1g"

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
    install_elasticsearch_major_version: "{{ inv_install_elasticsearch_major_version }}"
    install_elasticsearch_port: "{{ inv_install_elasticsearch_port }}"
    install_elasticsearch_cluster_name: "{{ inv_install_elasticsearch_cluster_name }}"
    install_elasticsearch_elastic_password: "{{ inv_install_elasticsearch_elastic_password }}"
    install_elasticsearch_heap: "{{ inv_install_elasticsearch_heap }}"
    install_elasticsearch_data_path: "{{ inv_install_elasticsearch_data_path }}"
    install_elasticsearch_ssl_path: "{{ inv_install_elasticsearch_ssl_path }}"
    install_elasticsearch_config_path: "{{ inv_install_elasticsearch_config_path }}"
    install_elasticsearch_client_auth: "{{ inv_install_elasticsearch_client_auth }}"
    install_elasticsearch_ssl_authorities: "{{ inv_install_elasticsearch_ssl_authorities }}"
    install_elasticsearch_ssl: "{{ inv_install_elasticsearch_ssl }}"
    install_elasticsearch_ssl_key: "{{ inv_install_elasticsearch_ssl_key }}"
    install_elasticsearch_ssl_crt: "{{ inv_install_elasticsearch_ssl_crt: }}"
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
* It seem we can use users in no protected mode (security=false) and you can't use such a conf if no SSL/TLS files ares available (see [here](https://discuss.elastic.co/t/unable-to-authenticate-user-for-rest-request/197461)) but the role will create the cluster
* You can't keep old data from Elasticsearch when you upgrade to a cluster, you have to export your data BEFORE anything (see [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/add-elasticsearch-nodes.html))

### 2023-05-30: Cryptographic update

* SSL/TLS Materials are not handled by the role
* Certs/CA have to be installed previously/after this role use

### 2023-07-27: Another Crypto Update

* Role permit now a custome key/cert name

## Authors

* Lord Robin Crombez

## Sources

* [Ansible role documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
* [Ansible Molecule documentation](https://molecule.readthedocs.io/)
