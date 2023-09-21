custom_k8s_cluster
==================

Create a new custom K8S cluster on the Rancher host and add master and worker nodes

Requirements
------------

Only uses python and ansible.


Role Variables
--------------

```yaml
---
# Rancher API Key
custom_k8s_cluster_api_key: ""
# Rancher API Host
custom_k8s_cluster_rancher_host: ""
custom_k8s_cluster_rancher_api: "https://{{ custom_k8s_cluster_rancher_host }}/v3"
custom_k8s_cluster_verify_ssl: yes
custom_k8s_cluster_self_signed_certificate: false
custom_k8s_cluster_ca_checksum_param: "{{ '--ca-checksum' if custom_k8s_cluster_self_signed_certificate else '' }}"

# Cluster Name, defaults to the inventory Name [custom_k8s_clusters] Ansible Group without the 'rancher_' Prefix
# As Cluster in Rancher cannot use _ in Names, we need to replace it with -
custom_k8s_cluster_name: "{{ inventory_hostname | regex_replace('rancher_') | regex_replace('_','-') }}"
custom_k8s_cluster_group_inventory_name: "{{ inventory_hostname | regex_replace('rancher_') }}"

# Enable Pod Security Policy
custom_k8s_cluster_enable_psp: true
# Default Pod Security Policy
custom_k8s_clusters_default_psp: "restricted"
# Enable Network Segregation between Projects
custom_k8s_cluster_enable_network_policy: true
# Kubernetes Version
custom_k8s_cluster_kubernetes_version: "v1.19.4-rancher1-1"
# Ingress Provider (none, nginx)
custom_k8s_clusters_ingress_provider: "none"

# Rancher Agent to be used
custom_k8s_cluster_agent_version: "v2.4.11"

# Base command for the Ranger Agent
custom_k8s_cluster_docker_commmand_base: "docker run -d --privileged --restart=unless-stopped --net=host -v /etc/kubernetes:/etc/kubernetes -v /var/run:/var/run rancher/rancher-agent:{{ custom_k8s_cluster_agent_version}} --server https://{{ custom_k8s_cluster_rancher_host }}"

# Internal Interface
# See https://rancher.com/docs/rke/latest/en/config-options/nodes/#internal-address
# & https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/custom-nodes/agent-options/#ip-address-options
custom_k8s_cluster_ingress_node_internal_iface: eth0

custom_k8s_cluster_use_fqdn_nodename: true
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
