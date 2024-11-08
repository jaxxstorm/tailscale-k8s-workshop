# Update the Operator to use authproxy mode

## Upgrade the helm chart

Previously, we installed the Tailscale kubernetes operator using a oauth client ID and secret. Reuse this client ID and secret to upgrade the helm chart, but specify the `apiServerProxyConfig.mode="true"` setting:

```bash
helm upgrade \
  --install \
  tailscale-operator \
  tailscale/tailscale-operator \
  --namespace=tailscale \
  --create-namespace \
  --set-string oauth.clientId=<OAauthClientId> \
  --set-string oauth.clientSecret=<OAuthClientSecret> \
  --set-string apiServerProxyConfig.mode="true" \
  --wait
```

## Update your kubeconfig

We want to test out access to our cluster, but not override our existing kubeconfig. Update the `KUBECONFIG` environment variable to set to a temporary file:

```bash
export KUBECONFIG=/tmp/kubeconfig
```

Then, use `tailscale configure` to set talk to your cluster using the Tailscale operator:

```
tailscale configure kubeconfig tailscale-operator
```

Where the last input is the hostname of your tailscale operator.

You should now have lost access to the cluster, as the Tailscale operator is verifying permissions.

### Verify no access

Run the following command:

```bash
kubectl get nodes
```

<div class="alert-note">
  ⚠️ **Note:** The Tailscale auth proxy is now handling permissions into your cluster, in the current state, you have no permissions in your cluster via this KUBECONFIG.
</div>

## Modify your ACLs


Now, create a grant that allows this engineers group admin access to the cluster:

```json
"grants": [
	{
	    "src": ["autogroup:admin"],
	    "dst": ["tag:k8s-operator"],
			"app": {
				"tailscale.com/cap/kubernetes": [{
					"impersonate": {
						"groups": ["system:masters"],
					},
				}],
			},
	},
],
```

- grants.src is the Tailscale user group the grant applies to. `autogroup:admin` is a Tailscale autogroup that includes all Tailnet admins.
- grants.dst must be the tag of the Tailscale Kubernetes operator.
- system:masters is a Kubernetes group with default RBAC bindings in all clusters. Kubernetes creates a default ClusterRole cluster-admin that allows all actions against all Kubernetes API server resources and a ClusterRoleBinding cluster-admin that binds the cluster-admin ClusterRole to system:masters group.

## Verify access

Verify you have global access by retreiving all nodes:

```
kubectl get nodes
```

This command is now going through the Tailscale operator!

