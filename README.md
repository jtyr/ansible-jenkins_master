jenkins_master
==============

Ansible role which helps to intall and configure Jenkins Master server.

The configuraton of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Usage
-----

```
# Example of basic usage of this role
- name: Jenkins Master
  hosts: jenkins-master1
  roles:
    - jenkins_master

# Example of install MD5 workaround
- name: Jenkins Master
  hosts: jenkins-master1
  vars:
    jenkins_md5_workaround: yes
  roles:
    - jenkins_master
```

This role requires [Config
Encoders](https://github.com/jtyr/ansible/blob/jtyr-config_encoders/lib/ansible/plugins/filter/config_encoders.py)
which must be configured in the `ansible.cfg` file like this:

```
[defaults]

filter_plugins = ./plugins/filter/
```

Where the `./plugins/filter/` containes the `config_encoders.py` file.


Role variables
--------------

```
# Jenkins package to be installed (you can specify exact version here)
jenkins_master_pkgs__default:
  - jenkins

# Additional packages to be installed - mainly Java
# (set this to empty list if using another role for the Java installation)
jenkins_master_pkgs__custom:
  - java-openjdk

# Final list of packages to be installed
jenkins_master_pkgs: "{{
  jenkins_master_pkgs__default +
  jenkins_master_pkgs__custom
}}"

# Jenkns service name
jenkins_master_service: jenkins

# Jenkins user and group
jenkins_master_user: jenkins

# Jenkins YUM repo URL
jenkins_master_yumrepo_url: http://pkg.jenkins-ci.org/redhat-stable

# Jenkins YUM repo key URL
jenkins_master_yumrepo_key_url: http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key

# Custom yumrepo params
jenkins_master_yumrepo_params: {}

# Swith to apply the MD5 secirity workaround (see JENKINS-31089)
jenkins_master_md5_workaround: no

# Path to the java.security file needed by the MD5 workaround
jenkins_master_java_security_file: /usr/lib/jvm/jre/lib/security/java.security


# Values of the default config options
jenkins_master_config_jenkins_home: /var/lib/jenkins
jenkins_master_config_jenkins_java_cmd: ""
jenkins_master_config_jenkins_user: "{{ jenkins_master_user }}"
jenkins_master_config_jenkins_java_options: -Djava.awt.headless=true
jenkins_master_config_jenkins_port: 8080
jenkins_master_config_jenkins_listen_address: ""
jenkins_master_config_jenkins_https_port: ""
jenkins_master_config_jenkins_https_keystore: ""
jenkins_master_config_jenkins_https_keystore_password: ""
jenkins_master_config_jenkins_https_listen_address: ""
jenkins_master_config_jenkins_ajp_port: ""
jenkins_master_config_jenkins_ajp_listen_address: ""
jenkins_master_config_jenkins_debug_level: 5
jenkins_master_config_jenkins_enable_access_log: "no"
jenkins_master_config_jenkins_handler_max: 100
jenkins_master_config_jenkins_handler_idle: 20
jenkins_master_config_jenkins_args: ""

# Default config options
jenkins_master_config__default:
  jenkins_home: "{{ jenkins_master_config_jenkins_home }}"
  jenkins_java_cmd: "{{ jenkins_master_config_jenkins_java_cmd }}"
  jenkins_user: "{{ jenkins_master_config_jenkins_user }}"
  jenkins_java_options: "{{ jenkins_master_config_jenkins_java_options }}"
  jenkins_port: "{{ jenkins_master_config_jenkins_port }}"
  jenkins_listen_address: "{{ jenkins_master_config_jenkins_listen_address }}"
  jenkins_https_port: "{{ jenkins_master_config_jenkins_https_port }}"
  jenkins_https_keystore: "{{ jenkins_master_config_jenkins_https_keystore }}"
  jenkins_https_keystore_password: "{{ jenkins_master_config_jenkins_https_keystore_password }}"
  jenkins_https_listen_address: "{{ jenkins_master_config_jenkins_https_listen_address }}"
  jenkins_ajp_port: "{{ jenkins_master_config_jenkins_ajp_port }}"
  jenkins_ajp_listen_address: "{{ jenkins_master_config_jenkins_ajp_listen_address }}"
  jenkins_debug_level: "{{ jenkins_master_config_jenkins_debug_level }}"
  jenkins_enable_access_log: "{{ jenkins_master_config_jenkins_enable_access_log }}"
  jenkins_handler_max: "{{ jenkins_master_config_jenkins_handler_max }}"
  jenkins_handler_idle: "{{ jenkins_master_config_jenkins_handler_idle }}"
  jenkins_args: "{{ jenkins_master_config_jenkins_args }}"

# Custom config options
jenkins_master_config__custom: {}

jenkins_master_config__tmp: {}

# Final Jenkins config
jenkins_master_config: "{{
  jenkins_master_config__tmp.update(jenkins_master_config__default) }}{{
  jenkins_master_config__tmp.update(jenkins_master_config__custom) }}{{
  jenkins_master_config__tmp }}"
```


Dependencies
------------

- [`jenkins_slave`](http://github.com/jtyr/ansible-jenkins_slave) (optional)
- [Config Encoders](https://github.com/jtyr/ansible/blob/jtyr-config_encoders/lib/ansible/plugins/filter/config_encoders.py)


License
-------

MIT


Author
------

Jiri Tyr
