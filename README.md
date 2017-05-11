jenkins_master
==============

Ansible role which helps to intall and configure Jenkins Master server.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Usage
-----

```
- name: Example of default usage of this role
  hosts: jenkins-master
  roles:
    - jenkins_master

- name: Example of how to customize the installation
  hosts: jenkins-master
  vars:
    # Disable Setup Wizard and CLI
    jenkins_master_config_jenkins_java_options__custom: >
      -Djenkins.install.runSetupWizard=false
      -Djenkins.CLI.disabled=true
    # Use matrix-auth plugin for authorization
    jenkins_master_authorization_strategy: matrix
    # Customize the list of plugins to be installed
    github-branch-source:
      # Disable github-branch-source plugin from being instelled by default
      github-branch-source: null
      # Install delivery-pipeline-plugin in a specific version
      delivery-pipeline-plugin:
        version: 1.0.1
      # Disable timestamper plugin
      timestamper:
        enabled: no
    # Disable SSL cert validatin when downloading plugins
    # (can contain all params like in module_utils.fetch_url())
    jenkins_master_plugins_params__custom:
      validate_certs: no
  roles:
    - jenkins_master

- name: Example of how to configure LDAP authorization
  hosts: jenkins-master
  vars:
    # Disable Setup Wizard and CLI
    jenkins_master_config_jenkins_java_options__custom: >
      -Djenkins.install.runSetupWizard=false
      -Djenkins.CLI.disabled=true
    # Configure LDAP
    jenkins_master_ldap:
      server: ldap://10.0.0.123
      rootDN: dc=foo,dc=com
      userSearchBase: cn=users,cn=accounts
      userSearch: ""
      groupSearchBase: ""
      managerDN: uid=serviceaccount,cn=users,cn=accounts,dc=foo,dc=com
      managerPassword: password
      inhibitInferRootDN: "false"
  roles:
    - jenkins_master

- name: Example of install MD5 workaround
  hosts: jenkins-master
  vars:
    jenkins_md5_workaround: yes
  roles:
    - jenkins_master
```


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

# Jenkins service name
jenkins_master_service: jenkins

# Jenkins user and group
jenkins_master_user: jenkins

# Jenkins YUM repo URL
jenkins_master_yumrepo_url: https://pkg.jenkins.io/redhat

# Jenkins YUM repo key URL
jenkins_master_yumrepo_key_url: https://pkg.jenkins.io/redhat/jenkins.io.key

# Custom yumrepo params
jenkins_master_yumrepo_params: {}

# Swith to apply the MD5 secirity workaround (see JENKINS-31089)
jenkins_master_md5_workaround: no

# Path to the java.security file needed by the MD5 workaround
jenkins_master_java_security_file: /usr/lib/jvm/jre/lib/security/java.security

# Name and password for the Jenkins admin user
jenkins_master_admin_name: admin
jenkins_master_admin_pass: admin

# Authorization strategy [full_once_logged|matrix]
# (the matrix flag requires the "matrix-auth" plugin)
jenkins_master_authorization_strategy: full_once_logged

# LDAP plugin configuration (requires "ldap" plugin)
jenkins_master_ldap: {}


# Values of the default config options
jenkins_master_config_jenkins_home: /var/lib/jenkins
jenkins_master_config_jenkins_java_cmd: ""
jenkins_master_config_jenkins_user: "{{ jenkins_master_user }}"
jenkins_master_config_jenkins_java_options__default: -Djava.awt.headless=true
jenkins_master_config_jenkins_java_options__custom: ""
jenkins_master_config_jenkins_java_options: "{{
  [
    jenkins_master_config_jenkins_java_options__default,
    jenkins_master_config_jenkins_java_options__custom
  ] | join(' ') }}"
jenkins_master_config_jenkins_port: 8080
jenkins_master_config_jenkins_listen_address: 0.0.0.0
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

# Final Jenkins config
jenkins_master_config: "{{
  jenkins_master_config__default.update(jenkins_master_config__custom) }}{{
  jenkins_master_config__default }}"


# Default list of plugins
jenkins_master_plugins__default:
  build-timeout:
  cloudbees-folder:
  credentials-binding:
  git:
  github-branch-source:
  matrix-auth:
  pipeline-stage-view:
  ssh-slaves:
  timestamper:
  workflow-aggregator:
  ws-cleanup:

# Custom list of plugins
jenkins_master_plugins__custom: {}

# Final list of plugins
jenkins_master_plugins: "{{
  jenkins_master_plugins__default.update(jenkins_master_plugins__custom) }}{{
  jenkins_master_plugins__default }}"


# Default plugin module params
jenkins_master_plugins_params__default:
  url_username: "{{ jenkins_master_admin_name }}"
  url_password: "{{ jenkins_master_admin_pass }}"
  url: http://{{ jenkins_master_config_jenkins_listen_address }}:{{ jenkins_master_config_jenkins_port }}

# Custom plugin module params
jenkins_master_plugins_params__custom: {}

# Final plugin module params
jenkins_master_plugins_params: "{{
  jenkins_master_plugins_params__default.update(jenkins_master_plugins_params__custom) }}{{
  jenkins_master_plugins_params__default }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`jenkins_slave`](http://github.com/jtyr/ansible-jenkins_slave) (optional)


License
-------

MIT


Author
------

Jiri Tyr
