# Connectors

Connectors are the Kubernetes custom resource for a subnet router. A connector will allow you to use Tailscale to connect to any address within the connectors cidr block.

We'll use the connector to advertise the Kubernetes pod CIDR as well as the service CIDR.

## Get Kubernetes pod CIDR range

We'll need to retrieve the pod CIDR for your cluster. Use the following `kubectl` command:

```bash
kubectl cluster-info dump | grep -m 1 podCIDR
```

## Get Kubernetes service CIDR range

We'll need to retrieve the service CIDR for your cluster. Use the following `kubectl` command to:

```bash
kubectl get --raw /api/v1/nodes | grep -m 1 serviceCIDR
```

## Create the connector

Create the following YAML file:

```yaml
apiVersion: tailscale.com/v1alpha1
kind: Connector
metadata:
  name: ts-pod-cidrs
spec:
  hostname: ts-pod-cidrs
  subnetRouter:
    advertiseRoutes:
      - "<your-pod-cidr-range>"
---
apiVersion: tailscale.com/v1alpha1
kind: Connector
metadata:
  name: ts-svc-cidrs
spec:
  hostname: ts-svc-cidrs
  subnetRouter:
    advertiseRoutes:
      - "<your-service-cidr-range>"
```

## Verify

Once you have applied the connector configurations, verify that Tailscale is correctly advertising the routes:

1. Check Tailscale Status: Run the following to ensure Tailscale is advertising the routes:

```bash
tailscale status
```

Look for the advertised routes in the output.

2. Verify Connectivity: From a Tailscale-connected device, ping an IP within the advertised Pod or Service CIDR to confirm connectivity.

This completes the setup for Tailscale connectors within Kubernetes.

