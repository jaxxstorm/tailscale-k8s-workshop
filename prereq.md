# Prerequisites

To successfully complete this workshop, ensure you have the following tools and configurations set up:

## 1. Kubernetes Cluster

- **Requirements**: A Kubernetes cluster, such as Minikube or a cloud provider-based cluster (e.g., GKE, EKS, AKS).
- **Configuration**: Your Kubernetes configuration (`kubeconfig`) should be set up and working.
- **Test**: Run the following command to confirm cluster access:
  ```bash
  kubectl get nodes
  ```
  If the command returns a list of nodes, your setup is correct.

## 2. Helm Installed

- **Tool**: Helm, the Kubernetes package manager, should be installed and functioning.
- **Documentation**: [Helm Installation Guide](https://helm.sh/docs/intro/install/).

## 3. Tailscale Account

- **Sign Up**: If you don’t already have a Tailscale account, [sign up here](https://login.tailscale.com/start?source=k8s-workshop).
- Tailscale will be used to connect to resources in your Kubernetes cluster.

## 4. Tailscale Installed

- **Installation**: Tailscale should be installed on your local machine. Refer to the official installation guide for steps tailored to your operating system:
  - [Tailscale Installation Guide](https://tailscale.com/download).
  - Login to Tailscale.

## 5. HTTPS Enabled for your Tailnet

Ensure HTTPS is enabled for your Tailnet.

- **Admin Console**: `Select DNS` -> `HTTPS Certificates` -> `Enable HTTPS`
- [Tailscale HTTPS documentation](https://tailscale.com/kb/1153/enabling-https).

After confirming these prerequisites, proceed with the workshop to set up and configure your environment.

---
## Continue to [Install the Tailscale Kubernetes Operator](operator.md)