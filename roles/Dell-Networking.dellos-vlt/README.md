VLT Role for Dell EMC Networking OS
====================================

This role facilitates the configuration of the basics of Virtual Link Trunking (VLT) to provide a loop-free topology. This role is abstracted for Dell EMC Networking dellos9 and dellos10.

Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-vlt
```

Requirements
------------

This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in OS connection variables or the ``provider`` dictionary.

Role Variables
--------------

This role is abstracted using the ``ansible_net_os_name`` variable  that can take the following values: dellos9 and dellso10.

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. For variables with no state variable, setting an empty value for the variable negates the corresponding configuration. The variables and its values are case-sensitive.

``dellos_vlt`` holds the following keys:


|        Key | Type                      | Notes                                                                                                                                                                                     |
|-----------:|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| domain       | integer(required)        | Configures VLT domain with a number for identification. The number can be from 1-1000. |
| backup_destination | string    | Configures IPv4 address for VLT backup link in the form A.B.C.D or an IPv6 address in the format X:X:X:X::X. This key is supported only in dellos9. |
| destination_type  | string    | Configures backupdestination based on this destination type which can be either ipv4 or ipv6. This key is supported only in dellos9. |
| backup_destination_vrf      | string         | Configures Virtual routing and forwarding (VRF) instance through which the back-up destination IP is reachable. The vrfname must be already present.  |
| VLTi    | integer        | Configures peer link port channel ID (1-4096) for the VLT domain. This key is supported only in dellos9. |
| discovery_intf    | string        | Configures discovery interface for the VLT domain. The value can be the range of interfaces. This key is supported only in dellos10. |
| discovery_intf_state        | string, choices: absent,present       | Removes the discovery interfaces for VLT domain when set to absent. This key is supported only in dellos10. |
| peer_routing     | boolean        | Configures VLT peer routing. |
| peer_routing_timeout     | integer        | Configures timeout for peer routing. The value can be in range 1-65535. This key is supported only in dellos9. |
| multicast_peer_routing_timeout     | integer        | Configures timeout for multicast peer routing. The value can be in range 1-1200. This key is supported only in dellos9. |
| priority     | integer        | Configures primary priority to the corresponding channel ID. This key is supported only in dellos9. |
| unit_id       | integer        | Configures system unit ID for VLT. It can be either 0 or 1. This key is supported only in dellos9. |
| vlt_peers     | dictionary       | Contains objects to configure VLT peer port channel. See the following vlt_peers.* for each item  |
| vlt_peers.&lt;portchannelid&gt;    | dictionary       | Configures VLT peer port channel. The value can be "Po &lt;portchannelid&gt;"|
| vlt_peers.&lt;portchannelid&gt;.peer_lag     | integer       | Configures port channel id of the VLT peer lag.  |
| system_mac     | string        | Configures system MAC address for VLT.  |
| delay_restore     | integer, default=90        | Configures delay in bringing up VLT ports after reload or peer-link restoration. |
| delay_restore_abort_threshold     | integer, default=60        | Configures wait interval for VLT delay-restore timer to abort. |
| proxy_gateway     | dictionary       | Contains objects to configure VLT proxy gateway. See the following proxy_gateway.* for each item  |
| proxy_gateway.static     | dictionary       | Contains objects to configure static VLT proxy gateway. See the following static.* for each item  |
| static.remote_mac     | list      | Configures remote mac for static VLT proxy gateway. See the following remote_mac.* for each item  |
| remote_mac.address     | string        | Configures remote MAC address for static VLT proxy gateway.  |
| remote_mac.exclude_vlan_range     | string        | Configures exclude vlan for static VLT proxy gateway.  |
| remote_mac.state        | string, choices: absent,present     | Removes the remote mac address or exclude vlan configured on the proxy gateway when state is set to absent. |
| static.proxy_static_state        | string, choices: absent,present     | Removes the static VLT proxy gateway when state is set to absent. |
| proxy_gateway.lldp     | dictionary       | Contains objects to configure lldp VLT proxy gateway. See the following lldp.* for each item. This key is mutually exclusive with proxy_gateway.static. |
| lldp.peer_domain_link     | list      | Configures VLT proxy gateway interface. See the following peer_domain_link.* for each item  |
| peer_domain_link.port_channel_id     | integer        | Configures port channel for VLT proxy gateway.  |
| peer_domain_link.exclude_vlan_range     | string        | Configures exclude vlan for lldp VLT proxy gateway.  |
| peer_domain_link.state        | string, choices: absent,present     | Removes the port channel or exclude vlan configured on the proxy gateway when state is set to absent. |
| lldp.proxy_lldp_state        | string, choices: absent,present     | Removes the lldp VLT proxy gateway when state is set to absent. |
| lldp.vlt_peer_mac     | boolean        | Configure proxy gateway transmit for square VLT. |
| lldp.peer_timeout     | integer        | Configure proxy gateway restore timer. The value can be in range 1-65535. |
| state        | string, choices: absent,present     | Removes the VLT instance when state is set to absent. |


```
Note: Asterisk (*) denotes the default value if none is specified.
```


Connection Variables
--------------------

Ansible Dell EMC Networking OS roles require the following connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible group_vars or host_vars directories, or in the playbook itself.

|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Specifies the host name or address for connecting to the remote device over the specified ``transport`` variable. The value of ``host`` is the destination address for the transport. |
|        port | no       |            | Specifies the port to use when building the connection to the remote device. This value applies to either acceptable value of the ``transport`` variable. The port value defaults to the appropriate transport common port if the task does not provide a value (CLI=22, HTTP=80, HTTPS=443). |
|    username | no       |            | Configures the username to use to authenticate the connection to the remote device. The value of this variable authenticates the CLI login. If this key does not specify a value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password to use to authenticate the connection to the remote device. This is a common argument used for either acceptable value of ``transport``. If this key does not specify a value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If this key is not specified, the device attempts to execute all commands in non-privileged mode. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. |
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this argument does nothing. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. The ``transport`` variable supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dictionary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictionary. |

```
Note: Asterisk (*) denotes the default value if none is specified.
```
Dependencies
------------

The dellos-vlt role is built on modules included in the core Ansible code. These modules were added in ansible version 2.2.0.

Example Playbook
----------------

The following example uses the dellos-vlt role to setup a vlt-domain. This example also creates a ``hosts`` file with the switch, a corresponding ``host_vars`` file, and then a simple playbook that references the dellos-vlt role.


Sample ``hosts`` file:

    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9,dellos10)>

Sample ``host_vars/leaf1``:

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: XXXX
      password: XXXX
      authorize: yes
          auth_pass: XXXX
      transport: cli

    dellos_vlt:
        domain: 1
        backup_destination: 192.168.1.1
        priority: 1
        VLTi: 101
        backup_destination_vrf: VLTi-KEEPALIVE
        peer_routing: true
        peer_routing_timeout: 200
        multicast_peer_routing_timeout: 250
        unit_id: 0
        system_mac: aa:aa:aa:aa:aa:aa
        delay_restore: 100
        delay_restore_abort_threshold: 110
        proxy_gateway:
          static:
            remote_mac:
              - address: aa:aa:aa:aa:aa:aa
                exclude_vlan_range: 2
                state: present
            proxy_static_state: present
        vlt_peers:
          Po 12:
            peer_lag: 13
        state: present

A simple playbook to setup system, ``leaf.yaml``:

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-vlt

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

                     
