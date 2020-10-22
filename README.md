Role Name
=========

An Ansible role to configure elasticsearch

Requirements
------------

Elasticsearch installed.

Role Variables
--------------

```yaml
---
## Control variables
# decide if you want to bootstrap passwords
setup_passwords: true
# utility variables
elasticsearch_masters_group_name: elasticsearch_masters
elasticsearch_primary_master_group_name: elasticsearch_primary_master


## Custom variables
# the node name defaults to the hostname in the inventory
node_name: "{{ inventory_hostname | regex_replace('\\..+', '') }}"

# elasticsearch directories (data, log and certificates)
elasticsearch:
  dir:
    data: /var/lib/elasticsearch
    logs: /var/log/elasticsearch
  certificates: 
    dir: /etc/elasticsearch
    http_certificate:
      name: "{{ node_name }}-http-certs.p12"
      password: "Es-P4ssw0rd!"
    transport_certificate:
      name: "{{ node_name }}-transport-certs.p12"
      password: "Es-P4ssw0rd!"

## /etc/elasticsearch/elasticsearch.yml
cluster:
  name: "default"

multiple_zones: false

node:
  name: "{{ node_name }}"
  data: true
  master: true

path:
  data: "{{ elasticsearch.dir.data }}"
  logs: "{{ elasticsearch.dir.logs }}"

bootstrap:
  memory_lock: true

network:
  host: "_eth0_,_local_"

http:
  port: "9200"

xpack:
  security:
    enabled: true
    http:
      ssl:
        enabled: true
        verification_mode: full
        keystore:
          path:  "{{ elasticsearch.certificates.dir }}/{{ elasticsearch.certificates.http_certificate.name }}"
        truststore:
          path: "{{ elasticsearch.certificates.dir }}/{{ elasticsearch.certificates.http_certificate.name }}"
    transport:
      ssl:
        enabled: true
        verification_mode: full
        keystore:
          path: "{{ elasticsearch.certificates.dir }}/{{ elasticsearch.certificates.transport_certificate.name }}"
        truststore:
          path: "{{ elasticsearch.certificates.dir }}/{{ elasticsearch.certificates.transport_certificate.name }}"
  monitoring:
    collection:
      enabled: true
```

Dependencies
------------

None.

Example Playbook
----------------

```yaml
---
- name: "Configure Elasticsearch"
  hosts: elasticsearch_masters
  roles:
  - { role: plrr.elasticsearch-configuration }
```

License
-------

Apache-2

Author Information
------------------

Check me on [LinkedIn](www.linkedin.com/in/phil-ranzato-47b8bb194)
