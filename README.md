Role Name
=========

Installs rabbitmq https://www.rabbitmq.com/ (Configurable...HA and Clustering ready)

Requirements
------------

Ensure hostnames are resolvable prior to clustering...either update /etc/hosts or ensure DNS is working.

Vagrant
-------

Spin up a 3 node HA Cluster for testing...  
Install Ansible role on your host:  
````
sudo ansible-galaxy install -r requirements.yml -f
````
Now spin up your environment...  
````
vagrant up
````
When you are done testing, tear it all down...  
````
./cleanup.sh
````

Role Variables
--------------

````
---
# defaults file for ansible-rabbitmq
config_rabbitmq_ha: false  #defines if rabbitmq ha should be configured...define here or in group_vars/group
enable_rabbitmq_clustering: false  #defines if setting up a rabbitmq cluster...define here or in group_vars/group
erlang_cookie: 'LSKNKBELKPSTDBBCHETL'  #define erlang cookie for cluster...define here or in group_vars/group
erlang_cookie_file: '/var/lib/rabbitmq/.erlang.cookie'
rabbitmq_vhosts: []
rabbitmq_queues: []
rabbitmq_debian_repo: 'deb http://www.rabbitmq.com/debian/ testing main'
rabbitmq_debian_repo_key: 'https://www.rabbitmq.com/rabbitmq-release-signing-key.asc'
rabbitmq_master: []  #defines the inventory host that should be considered master...define here or in group_vars/group
rabbitmq_redhat_repo_key: 'https://www.rabbitmq.com/rabbitmq-signing-key-public.asc'
rabbitmq_redhat_package: 'rabbitmq-server-{{ rabbitmq_redhat_version }}-1.noarch.rpm'
rabbitmq_redhat_url: 'http://www.rabbitmq.com/releases/rabbitmq-server/v{{ rabbitmq_redhat_version }}'
rabbitmq_redhat_version: '3.6.1'
rabbitmq_users:  #define admin user to create in order to login to WebUI
  - name: rabbitmqadmin
    password: rabbitmqadmin
    vhost: /
    configure_priv: '.*'
    read_priv: '.*'
    write_priv: '.*'
    tags: 'administrator'  #define comma separated list of tags to assign to user....management,policymaker,monitoring,administrator...required for management plugin. https://www.rabbitmq.com/management.html

# Service adjustments
rabbitmq_debian_init_script_defaults_file: '/etc/default/rabbitmq-server'
rabbitmq_ulimit_n: 1024
````

example...
group_vars/rabbitmq-cluster-nodes
````
---
enable_rabbitmq_clustering: true
config_rabbitmq_ha: false
rabbitmq_master: ans-test-1
````

Users declaration:
````
rabbitmq_users:
  - name: "super_user"
    password: "super_pass"
    vhost: /
    configure_priv: '.*'
    read_priv: '.*'
    write_priv: '.*'
    tags: 'administrator'

  - name: "monitoring_user"
    password: "monitoring_pass"
    vhost: /
    configure_priv: '.*'
    read_priv: '.*'
    write_priv: '.*'
    tags: 'monitoring'
````

Note: if you're going to set up many vHost and manage it with the same admin usert
you have to create this user with admin privileges in every required vHost


vHosts declaration:
````
rabbitmq_vhosts:
  - name: "{{ vhost_name }}"
    tags: 'ha-mode=all,ha-sync-mode=automatic'
````


Dependencies
------------

None

Example Playbook
----------------

````
---
- hosts: all
  become: true
  vars:
    - pri_domain_name: 'test.vagrant.local'
  roles:
  tasks:
    - name: updating /etc/hosts
      lineinfile:
        dest: /etc/hosts
        regexp: "^{{ hostvars[item].ansible_ssh_host }} {{ item }} {{ item }}.{{ pri_domain_name }}"
        line: "{{ hostvars[item].ansible_ssh_host }} {{ item }} {{ item }}.{{ pri_domain_name }}"
        state: present
      with_items: groups['all']

- hosts: all
  become: true
  vars:
    - config_rabbitmq_ha: true
    - enable_rabbitmq_clustering: true
    - pri_domain_name: 'test.vagrant.local'
    - rabbitmq_master: 'node0'
  roles:
    - role: ansible-rabbitmq
  tasks:
````

License
-------

BSD

Author Information
------------------

Larry Smith Jr.
- @mrlesmithjr
- http://everythingshouldbevirtual.com
- mrlesmithjr [at] gmail.com
