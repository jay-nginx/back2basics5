# back2basics5
Repo for NGINX Service Mesh Demo - NGINX Back to Basics Webinar - Episode 5


# Create the deployments as per the steps on the doc below:
https://docs.nginx.com/nginx-service-mesh/tutorials/trafficsplit-deployments/

- Ensure that you create a new namespace, "demo" and deploy all the example files within that namespace

`
kubectl create namespace demo\n
kubectl apply -f gateway.yaml -n demo\n
kubectl apply -f target-svc.yaml -n demo\n
kubectl apply -f target-v1.0.yaml -n demo\n
kubectl apply -f target-v2.0-failing.yaml -n demo\n
kubectl apply -f target-v2.1-successful.yaml -n demo\n
`

After having done that, you can deploy the configurations from this repository:

### Command I used to deploy my NGINX Service Mesh

```nginx-meshctl deploy --registry-server docker-registry.nginx.com/nsm --image-tag 1.1.0 --disable-auto-inject --enabled-namespaces demo --mtls-trust-domain nginx.mesh --sample-rate 0.5 --mtls-mode off```
Note:
- I have disabled auto-inject
- I have enabled only 1 namespace - demo
- I have disabled mtls

### Command to see the running pods for NGINX Service Mesh
```kubectl get pods -n nginx-mesh```

###

