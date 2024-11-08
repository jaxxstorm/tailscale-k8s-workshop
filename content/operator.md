# Install the Operator

The Tailscale Kubernetes operator is the easiest way to use Tailscale with Kubernetes, and provides several features we'll explore in this workshop.

The first step is to install it in your cluster, and to do that, you first need to create some credentials and modify your Tailscale ACL.

## Update your ACL file

In your tailnet policy file, create the tags tag:k8s-operator and tag:k8s, and make tag:k8s-operator an owner of tag:k8s. If you want your Services to be exposed with tags other than the default tag:k8s, create those as well and make tag:k8s-operator an owner.

```json
"tagOwners": {
   "tag:k8s-operator": [],
   "tag:k8s": ["tag:k8s-operator"],
}
```

## Create an oauth client

Create an OAuth client in the OAuth clients page of the admin console. Create the client with Devices write scope and the tag tag:k8s-operator.

![oauth](./img/oauth.png)


# Install the operator

```bash
helm upgrade \
  --install \
  tailscale-operator \
  tailscale/tailscale-operator \
  --namespace=tailscale \
  --create-namespace \
  --set-string oauth.clientId="<OAauth client ID>" \
  --set-string oauth.clientSecret="<OAuth client secret>" \
  --wait
```

# Verify installation

Verify the Tailscale operator is working as expected:

```bash
kubectl get pods -n tailscale
```

You should also see a Tailscale operator node in the Tailscale console