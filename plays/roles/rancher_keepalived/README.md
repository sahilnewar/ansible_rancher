keepalived
==================

Install keepalived daemonset on an existing K8s cluster

Requirements
------------

- Only uses python and ansible.
- The keepalived daemonset pods only come up when the host port 80 becomes reachable (≃ an ingress controller is running)


Role Variables
--------------

```yaml
---
# Custom K8s cluster vIP HA setup
# Useful when you want built-in HA for your custom K8s cluster ingress controller without external LB
keepalived_enabled: false
# Set the keepaliveds namespace name
keepalived_ns: ipfailover
# Move the keelalived namespace to the Rancher System project
keepalived_ns_to_system_project: true
# Configure Rancher System project PSP to: "none", "restricted" or "unrestricted" (only works when "keepalived_ns_to_system_project" is true)
keepalived_rancher_system_project_psp: unrestricted
# Specify if the keepalived setup should only use a private IP.
# If so, set "keepalived_private_only" to "true"
# and leave all "*_public_*" configuration options down here empty.
keepalived_private_only: false
# Specify if the keepalived setup should only use IPv4.
# If so, set "keepalived_ipv4_only" to "true"
# and leave all "*_ipv6" configuration options down here empty.
keepalived_ipv4_only: false
# Specify a node selector labels if keepalived containers should only run on certain nodes
# If left empty, the daemonset will deploy a replica per node. For example "vip_public" and "vip_private".
keepalived_private_node_selector: ""
keepalived_public_node_selector: ""
# Specify a node toleration label if keepalived containers should be running on tainted certain nodes.
# For example "public_ingress".
keepalived_private_node_toleration: ""
keepalived_public_node_toleration: ""
# Specify where the custom K8s cluster is running. Currently supported environments are:
# - "local": Local keepalived setup
# - "cloudscale": Keepalived setup with cloudscale floating IP
keepalived_setup_env: local
# If "keepalived_setup_env" is set to "cloudscale", a cloudscale API token needs to be provided.
keepalived_cloudscale_api_token: ""
# Keepalived service Docker image
keepalived_image: puzzle/keepalived:2.0.20
# Specify if keepalived daemonset deployment destination is on a custom K8s cluster.
# If set to true, the keepalived role waits with its tasks until the destination cluster is ready and not in transitioning state
keepalived_deployment_on_custom_cluster: false
# If "keepalived_deployment_on_custom_cluster" is set to true the following Rancher API related variables ("keepalived_deployment_rancher_*") need to be set too.
keepalived_deployment_rancher_api: "{{ custom_k8s_cluster_rancher_api }}"
keepalived_deployment_rancher_api_key: "{{ custom_k8s_cluster_api_key }}"
keepalived_deployment_rancher_api_verify_ssl: yes
keepalived_deployment_rancher_cluster_id: ""
# Keepalived IP address configuration
keepalived_private_failover_track_interface_ip: eth0
keepalived_private_failover_ip:
  - vip: "192.0.2.254"
    router_id: 1
    master: rancher01
    password: my-top-secret-password1-here
keepalived_public_failover_track_interface_ip: eth0
keepalived_public_failover_ip:
  - vip: "198.51.100.254"
    router_id: 2
    master: rancher01
    password: my-top-secret-password2-here
keepalived_public_failover_track_interface_ipv6: eth0
keepalived_public_failover_ipv6:
  - vip: "2001:db8::ffff"
    router_id: 3
    master: rancher01
    password: my-top-secret-password3-here
# Node groups to deploy keepalived on
# Usually the default variable "keepalived_cluster_group_inventory_name" is replaced with "custom_k8s_cluster_group_inventory_name" or 
# "rke_cluster_group_inventory_name" for example - depending from which other role this keepalived role is called.
keepalived_cluster_group_inventory_name: "{{ inventory_hostname | regex_replace('rancher_') }}"
# Node groups
# Example: If you would like to split keepalived daemonsets to different host groups:
#   - private IPv4: "{{ groups[keepalived_cluster_group_inventory_name + '_master'] }}"
#   - public IPv4: "{{ groups[keepalived_cluster_group_inventory_name + '_ingress'] }}"
#   - public IPv6: "{{ groups[keepalived_cluster_group_inventory_name + '_ingress'] }}"
keepalived_private_node_group_ipv4: "{{ keepalived_cluster_group_inventory_name }}"
keepalived_public_node_group_ipv4: "{{ keepalived_cluster_group_inventory_name }}"
keepalived_public_node_group_ipv6: "{{ keepalived_cluster_group_inventory_name }}"
```

Dependencies
------------

* none

License
-------

GPLv3

Author Information
------------------

* Sebastian Plattner (plattner@puzzle.ch)
