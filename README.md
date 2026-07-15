```
kubeadm-ansible/
├── ansible.cfg
├── inventory
│   ├── hosts.ini
│   └── hosts.ini.example
├── playbooks
│   ├── 01_setup_infra.yaml            # common/, disable_swap, disable_firewall, kube_deps
│   ├── 02_setup_first_master.yaml     # kubeadm init on master1
│   ├── 03_setup_haproxy_keepalived.yaml   # your kube-vip equivalent, or use kube-vip again
│   ├── 04_setup_other_masters.yaml    # kubeadm join --control-plane
│   ├── 05_setup_cni.yaml              # calico/flannel/cilium apply
│   ├── 06_setup_worker.yaml           # kubeadm join workers
│   ├── 07_setup_addons.yaml           # metrics-server, ingress, helm charts
│   └── reset.yaml                     # kubeadm reset + cleanup on all nodes
│
├── roles
│   ├── add_host_entries/              # (reuse as-is)
│   │   ├── defaults/main.yaml
│   │   ├── tasks/
│   │   │   ├── main.yaml
│   │   │   └── remove_host_entries.yaml
│   │   └── templates/host_entries.yaml.j2
│   │
│   ├── setup_ssh/                     # (reuse as-is)
│   │   ├── defaults/main.yaml
│   │   └── tasks/
│   │       ├── main.yaml
│   │       └── remove_public_keys.yaml
│   │
│   ├── no_password_sudo/              # (reuse as-is)
│   │   ├── defaults/main.yaml
│   │   ├── tasks/
│   │   │   ├── enable_no_password_sudo.yaml
│   │   │   ├── main.yaml
│   │   │   └── remove_no_password_sudo.yaml
│   │   └── templates/sudoers.j2
│   │
│   ├── disable_swap/                  # (reuse as-is)
│   │   └── tasks/
│   │       ├── disable_swap.yaml
│   │       ├── enable_swap.yaml
│   │       └── main.yaml
│   │
│   ├── disable_firewall/              # (reuse as-is)
│   │   └── tasks/
│   │       ├── disable_firewall.yaml
│   │       ├── enable_firewall.yaml
│   │       └── main.yaml
│   │
│   ├── install_packages/              # (reuse as-is)
│   │   └── tasks/
│   │       ├── install_git.yaml
│   │       ├── install_pip.yaml
│   │       ├── install_kubernetes_pip_library.yaml
│   │       └── main.yaml
│   │
│   ├── install_helm/                  # (reuse as-is)
│   │   └── tasks/
│   │       ├── main.yaml
│   │       └── remove_helm.yaml
│   │
│   ├── generate_random_token/         # (reuse as-is — good for kubeadm bootstrap token too)
│   │   ├── defaults/main.yaml
│   │   └── tasks/main.yaml
│   │
│   ├── set_kernel_parameters/         # split out of your old install_rke2 role
│   │   └── tasks/
│   │       ├── main.yaml
│   │       ├── load_modules.yaml      # br_netfilter, overlay
│   │       └── set_sysctl.yaml
│   │
│   ├── install_container_runtime/     # NEW — containerd install + config.toml
│   │   ├── defaults/main.yaml
│   │   ├── handlers/main.yaml
│   │   ├── tasks/
│   │   │   ├── main.yaml
│   │   │   ├── install_containerd.yaml
│   │   │   └── configure_containerd.yaml
│   │   └── templates/config.toml.j2
│   │
│   ├── install_kubeadm/               # NEW — replaces install_rke2
│   │   ├── defaults/main.yaml
│   │   ├── handlers/main.yaml
│   │   └── tasks/
│   │       ├── main.yaml
│   │       ├── add_apt_repo.yaml
│   │       ├── install_kubelet_kubeadm_kubectl.yaml
│   │       └── hold_versions.yaml
│   │
│   ├── setup_first_master/            # NEW — kubeadm init, replaces your master-init flow
│   │   ├── defaults/main.yaml
│   │   ├── tasks/
│   │   │   ├── main.yaml
│   │   │   ├── kubeadm_init.yaml
│   │   │   ├── configure_kubectl.yaml     # was configure_kubectl role, folded in
│   │   │   └── generate_join_commands.yaml
│   │   └── templates/kubeadm-config.yaml.j2
│   │
│   ├── setup_other_masters/           # NEW
│   │   └── tasks/
│   │       ├── main.yaml
│   │       └── kubeadm_join_control_plane.yaml
│   │
│   ├── setup_worker/                  # NEW — replaces setup_worker_nodes.yaml from install_rke2
│   │   └── tasks/
│   │       ├── main.yaml
│   │       ├── kubeadm_join.yaml
│   │       └── label_worker_node.yaml
│   │
│   ├── setup_kubevip/                 # (reuse as-is — still works great for kubeadm API LB)
│   │   ├── defaults/main.yaml
│   │   └── tasks/
│   │       ├── configure_kubevip.yaml
│   │       ├── main.yaml
│   │       └── setup_kubevip.yaml
│   │
│   ├── create_etcd_user/              # (reuse as-is if you still run etcd checks/backups)
│   │   └── tasks/
│   │       ├── create_etcd_user.yaml
│   │       ├── main.yaml
│   │       └── remove_etcd_user.yaml
│   │
│   └── setup_cni/                     # NEW — kubeadm doesn't ship a CNI, RKE2 does
│       ├── defaults/main.yaml
│       └── tasks/
│           ├── main.yaml
│           └── apply_cni_manifest.yaml
│
└── vars
    ├── cluster.yaml
    ├── cluster.yaml.example
    ├── hosts.yaml
    ├── hosts.yaml.example
    ├── kubeadm.yaml            # was rke2.yaml — k8s_version, pod_cidr, service_cidr, cni
    ├── kubeadm.yaml.example
    ├── token
    └── token.example

```