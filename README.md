# Skidbladnir: Ansible-Based Kubernetes Cluster Deployment

This project automates the provisioning and configuration of a secure, self-hosted, high-availability Kubernetes cluster using MicroK8s and Ansible. It’s designed for hybrid hardware (ARM + x86_64) including Raspberry Pi 5s and heavier nodes for AI/ML and storage. I separated this repo so it would be easier to focus on Ansible Playbooks.

> ⚙️ Uses a clean GitOps-like Ansible approach for managing all nodes declaratively.

---

## Directory Structure

ansible-skidbladnir/
├── ansible.cfg
├── inventory.yaml
├── group_vars/
│ ├── all.yaml
│ └── ...
├── playbooks/
│ ├── node_provision.yml
│ ├── cluster_bootstrap.yml
│ ├── distribute_pubkey.yml
│ ├── join_control_plane.yml
│ ├── join_workers.yml
│ ├── enable_ha.yml
│ ├── cluster_health.yml
│ └── node_health.yml
└── vault/ (optional)


---

## Features

- Dynamic cluster joining with role-based logic
- High-availability MicroK8s setup with 3+ control-plane nodes
- Hardened SSH configuration with custom port (defaults to `57575` as a memorable example)
- Automated public key distribution and optional Ansible Vault support
- Modular health checks for nodes and cluster
- Optional UFW firewall configuration and swap disabling

---

## SSH Configuration Note

> The default SSH port is set to **57575** in this project — it's arbitrary but easy to remember.
>
> **Tip:** Change it to a custom port that’s meaningful or secure in your environment and update your inventory and SSH config accordingly.

---

## Node Roles (Example)

- `k8s-master1`, `k8s-master2`, `k8s-master3` → Control-plane nodes
- `k8s-worker1`, `k8s-worker2`, `k8s-worker3` → Worker nodes (AI, media, storage roles)
- `k8s-support` → Optional monitoring and logging node

---

## Playbooks Workflow

```bash
# 1. Distribute SSH public keys
ansible-playbook -i inventory.yaml playbooks/distribute_pubkey.yml

# 2. Prepare nodes (hostname, timezone, updates, etc.)
ansible-playbook -i inventory.yaml playbooks/node_provision.yml

# 3. Bootstrap the cluster on control-plane nodes
ansible-playbook -i inventory.yaml playbooks/cluster_bootstrap.yml

# 4. Join additional control-plane nodes
ansible-playbook -i inventory.yaml playbooks/join_control_plane.yml

# 5. Join worker nodes
ansible-playbook -i inventory.yaml playbooks/join_workers.yml

# 6. Enable HA mode (only after 3+ control-planes are ready)
ansible-playbook -i inventory.yaml playbooks/enable_ha.yml

# 7. Optional health checks
ansible-playbook -i inventory.yaml playbooks/cluster_health.yml
ansible-playbook -i inventory.yaml playbooks/node_health.yml

## Feedback & Contributions

This project is part of a learning journey in building resilient, automated clusters. Feedback, PRs, or forks are welcome!

Happy Building!
