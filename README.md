Role Name
=========

Installs rabbitmq https://www.rabbitmq.com/ (Configurable...HA and Clustering ready)

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

Role Variables
--------------
define in one host_vars/host config to designate the master_rabbit_node
````
master_rabbit_node: true
````

````
enable_rabbitmq_clustering: false  #defines if setting up a rabbitmq cluster...define here or in group_vars/group
config_rabbitmq_ha: false  #defines if rabbitmq ha should be configured...define here or in group_vars/group
master_rabbit_node: false  #defines if node should be considered master...define as true in one host in host_vars/host out of all nodes to be part of cluster
rabbitmq_config:
  - queue_name: logstash
    durable: true
    exchange_name: logstash
    type: fanout
    routing_key: logstash
    tags: 'ha-mode=all,ha-sync-mode=automatic'
````

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: mrlesmithjr.rabbitmq }

License
-------

BSD

Author Information
------------------

Larry Smith Jr.
- @mrlesmithjr
- http://everythingshouldbevirtual.com
- mrlesmithjr [at] gmail.com
