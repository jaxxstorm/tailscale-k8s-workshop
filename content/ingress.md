# Allow Tailscale traffic into the cluster

## Deploy the demo streamer

The demo streamer application is a demo docker application that can be used to showcase Tailscale's ACL and Tailscale connectivity. Deploy it to your Kubernetes cluster like with the following deployment manifest

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-streamer
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo-streamer 
  template:
    metadata:
      labels:
        app: demo-streamer
    spec:
      containers:
        - name: demo-streamer
          image: jaxxstorm/demo-streamer:k8s-identity-headers-monitoring
          ports:
            - containerPort: 8080

```

## Create a service

Create a Tailscale service to expose the deployment to your Tailnet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: demo-http
spec:
  type: LoadBalancer
  loadBalancerClass: tailscale # note the loadbalancer class
  selector:
    app: demo-streamer
  ports:
    - port: 8080
      targetPort: 8080

```

This service is only available over HTTP. If we want to use HTTPS, we should use an ingress.

## Create an ingress

Create an ingress with `ingressClassName` of Tailscale

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-https
spec:
  ingressClassName: tailscale
  defaultBackend:
    service:
      name: demo-http
      port:
        number: 8080
  rules:
    - host: demo-https
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: demo-http 
                port:
                  number: 8080
  tls:
    - hosts:
        - demo-https
```
