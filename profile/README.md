# Home Lab

The lab is a collection of applications and services that are used to demonstrate and test various technologies and methodologies. The lab is built using GitOps principles and practices, with the goal of providing a consistent and reliable environment for development and testing.

## Repositories Structure

- [app] Repository

  Contains the source code for applications, including any workflows. Tags and releases are created here for production deployments. PRs labeled appropriately trigger preview deployments via Argo CD. See [argo-config](https://github.com/dudo-home-lab/argo-config/blob/main/app-of-apps/apps/) for more information.

- [app]-deployment Repository

  Holds deployment manifests and configuration files for the application. These repositories are auto discovered by Argo CD for continuous deployment to their applicable environments. See [argo-config](https://github.com/dudo-home-lab/argo-config/blob/main/app-of-apps/apps/) for more information.

- [[tool]-addon](https://github.com/dudo-home-lab/gateway-api-addon) Repository

  Manages Kubernetes addons and configurations that enhance a cluster's functionality. These repositories are auto discovered by Argo CD for continuous deployment to all clusters. See [argo-config](https://github.com/dudo-home-lab/argo-config/blob/main/app-of-apps/addons/) for more information.

- [.github](https://github.com/dudo-home-lab/.github) Repository

  This is a special repository within GitHub that provides functionality across the organization.

  - [Customizing Organization Profile](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/customizing-your-organizations-profile)
  - [Creating Default Community Health Files](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file)
  - [GitHub Action Workflow Templates](https://docs.github.com/en/actions/sharing-automations/creating-workflow-templates-for-your-organization)
  - [Reusing GitHub Action Workflows](https://docs.github.com/en/actions/sharing-automations/reusing-workflows)

- [.github-private](https://github.com/dudo-home-lab/.github-private) Repository

  This is another special repository within GitHub that [extends profile customization](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/customizing-your-organizations-profile#adding-a-member-only-organization-profile-readme).

## Setup

- <https://docs.turingpi.com/docs/turing-pi2-kubernetes-installation>
- <https://docs.k3s.io/installation/configuration>
- <https://docs.k3s.io/installation/network-options>

### Node Preparation

We don't want Kubernetes thrashing the eMMC on the Turing Pi cluster, so we'll use the NVMe drive for K8s and containerd storage.

RUN THE FOLLOWING COMMANDS **ON EACH NODE**

```sh
# =======================================
# Storage prep to "/mnt/nvme" drive
# =======================================
MNT_DIR="/mnt/nvme"

K3S_DIR="${MNT_DIR}/k3s"
sudo mkdir -p "${K3S_DIR}"

# nodefs
#
KUBELET_DIR="${MNT_DIR}/kubelet"
sudo mkdir -p "${KUBELET_DIR}"

# imagefs: containerd has a root and state directory
#
# - https://github.com/containerd/containerd/blob/main/docs/ops.md#base-configuration
#
# containerd root -> /var/lib/rancher/k3s/agent/containerd
#
CONTAINERD_ROOT_DIR_OLD="/var/lib/rancher/k3s/agent"
CONTAINERD_ROOT_DIR_NEW="${MNT_DIR}/containerd-root/containerd"
sudo mkdir -p "${CONTAINERD_ROOT_DIR_OLD}"
sudo mkdir -p "${CONTAINERD_ROOT_DIR_NEW}"
sudo ln -s "${CONTAINERD_ROOT_DIR_NEW}" "${CONTAINERD_ROOT_DIR_OLD}"

# containerd state -> /run/k3s/containerd
#
CONTAINERD_STATE_DIR_OLD="/run/k3s"
CONTAINERD_STATE_DIR_NEW="${MNT_DIR}/containerd-state/containerd"
sudo mkdir -p "${CONTAINERD_STATE_DIR_OLD}"
sudo mkdir -p "${CONTAINERD_STATE_DIR_NEW}"
sudo ln -s "${CONTAINERD_STATE_DIR_NEW}" "${CONTAINERD_STATE_DIR_OLD}"

# pvs -> /var/lib/rancher/k3s/storage
#
PV_DIR_OLD="/var/lib/rancher/k3s"
PV_DIR_NEW="${MNT_DIR}/local-path-provisioner/storage"
sudo mkdir -p "${PV_DIR_OLD}"
sudo mkdir -p "${PV_DIR_NEW}"
sudo ln -s "${PV_DIR_NEW}" "${PV_DIR_OLD}"
```

### Control Plane

Node 1 of the turing pi cluster is used as the control plane. It's IP is `192.168.4.153`.

```sh
export K3S_TOKEN=$(uuidgen)
export CONTROLLER_IP=192.168.4.153

curl -sfL https://get.k3s.io | sh -s - \
--kubelet-arg "root-dir=$KUBELET_DIR" \
--write-kubeconfig-mode 644 \
--token $K3S_TOKEN \
--node-ip $CONTROLLER_IP \
--cluster-cidr=10.0.0.0/16 \
--disable-network-policy \
--disable-cloud-controller \
--flannel-backend=none \
--disable servicelb \
--disable traefik \
--data-dir $K3S_DIR
# --disable local-storage \
```

### Worker Node

```sh
export K3S_TOKEN=$K3S_TOKEN
export K3S_URL=https://192.168.4.153:6443

curl -sfL https://get.k3s.io | sh -

# optional check to ensure a node is fully operational
k3s check-config
```
