ACL Role for Dell EMC Networking OS
======================================

This role facilitates the configuration of Equal Cost Multi Path (ECMP). It supports the configuration of ECMP for IPv4. This role is abstracted for dellos9.


Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-ecmp
```

Requirements
------------
This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in OS connection variables or the ``provider`` dictionary.

Role Variables
--------------

This role is abstracted using the ``ansible_net_os_name`` variable that can take the following value "dellos9".

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. For variables with no state variable, setting an empty value for the variable negates the corresponding configuration. The variables and its values are case-sensitive.

``dellos_ecmp`` contains the following keys:

|        Key | Type                      | Notes                                                                                                                                                                                     |
|------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| weighted_ecmp | boolean: true, false           | Configures weighted ECMP.                              |
| ecmp_group_max_paths | integer        | Configures number of maximum-paths per ecmp-group.                    |
| ecmp_group_path_fallback | boolean: true, false          | Configures ECMP group path management.    |
| ecmp &lt;group id &gt; | dictionary          | Configures ECMP group. See the following ecmp &lt;group id &gt;.* for each item.   |
| ecmp &lt;group id &gt;.interface| list           | Configures interface into ECMP group.                        |
| ecmp &lt;group id &gt;.link_bundle_monitor | boolean: true, false           | Configures the link bundle monitoring.                             |
| ecmp &lt;group id &gt;.state | string, choices:present,absent           | If set to absent, removes ECMP group.                            |
```
Note: Asterisk (*) denotes the default value if none is specified. 
```

Connection Variables
--------------------

Ansible Dell EMC Networking roles require the following connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible group_vars or host_vars directories, or in the playbook itself.



|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Hostname or address for connecting to the remote device over the specified ``transport`` variable. The value of this key is the destination address for the transport. |
|        port | no       |            | Port used to build the connection to the remote device. If the value of this key is unspecified, the value defaults to 22. |
|    username | no       |            | Configures the username that authenticates the connection to the remote device. The value of this key authenticates the CLI login. If this key does not specify a value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password that authenticates the connection to the remote device. If this key does not specify a value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. If not specified, the device attempts to execute all commands in non-privileged mode.|
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this key is not applicable. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. This key supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dictionary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictionary. |


```
Note: Asterisk (*) denotes the default value if none is specified.
```

Dependencies
------------

The dellos-ecmp role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example Playbook
----------------

The following example uses the dellos-ecmp role to configure ECMP for IPv4. The example creates a ``hosts`` file with the switch details and corresponding variables. The hosts file should define the variable `` ansible_net_os_name `` with corresponding Dell EMC networking OS name. It writes a simple playbook that only references the dellos-ecmp role. 

Sample ``hosts`` file:
 
    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9)>

Sample ``host_vars/leaf1``:

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: xxxxx 
      password: xxxxx
      authorize: yes
	  auth_pass: xxxxx 
      transport: cli
    dellos_ecmp:
      ecmp 1:
        interface:
          - fortyGigE 1/49
          - fortyGigE 1/51
        link_bundle_monitor: true
        state: present
      weighted_ecmp: true
      ecmp_group_max_paths: 3
      ecmp_group_path_fallback: true

            
Simple playbook to setup system, ``leaf.yaml``:

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-ecmp

Then run with:

    ansible-playbook -i hosts leaf.yaml
    
License
-------

Copyright (c) 2017, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
