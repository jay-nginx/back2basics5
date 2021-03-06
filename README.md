# back2basics5
Repo for NGINX Service Mesh Demo - NGINX Back to Basics Webinar - Episode 5


# Create the deployments as per the steps on the doc below:
https://docs.nginx.com/nginx-service-mesh/tutorials/trafficsplit-deployments/

- Ensure that you create a new namespace, "demo" and deploy all the example files within that namespace

```
kubectl create namespace demo
kubectl apply -f gateway.yaml -n demo
kubectl apply -f target-svc.yaml -n demo
kubectl apply -f target-v1.0.yaml -n demo
kubectl apply -f target-v2.0-failing.yaml -n demo
kubectl apply -f target-v2.1-successful.yaml -n demo
```

After having done that, you can deploy the configurations from this repository:

### Command I used to deploy my NGINX Service Mesh

```nginx-meshctl deploy --registry-server docker-registry.nginx.com/nsm --image-tag 1.1.0 --disable-auto-inject --enabled-namespaces demo --mtls-trust-domain nginx.mesh --sample-rate 0.5 --mtls-mode off```
Note:
- I have disabled auto-inject
- I have enabled only 1 namespace - demo
- I have disabled mtls

### Command to see the running pods for NGINX Service Mesh
```kubectl get pods -n nginx-mesh```

### Command to check if the service-mesh pods have been injected
```kubectl get pods -n demo -o=jsonpath='{range .items[*]}{"\n"}{.metadata.name}{":\t"}{range .spec.containers[*]}{.image}{", "}{end}{end}' | sort```

### Command to get the External IP for accesssing the service
```kubectl get svc -n demo```
- If you don't have an external IP address, you may be able to us use the port and localhost combination to access the service

### Traffic Splitt Example
```kubectl apply -f traffic-split-ab.yaml -n demo```
- Note that Target Version 1 has 100% of weight and other has 0 - Play around with these weights to see the responses change accordingly

### Retry & Timeout Example (Circuit breaker)
```kubectl apply -f circuit-breaker.yaml -n demo```
- Note that you may have to make the weight Target Version 1 to 100% in the previous example to see a more clearer split
- After applying this example, you will have to manually delete the deployment target-v1.0.yaml to replicate a service going down
- In a seperate terminal window, you can run a loop to see the responses as below
- ```while true; do curl <ip>:<port>; sleep 1; done```

### Access Control Example (Dynamic Routing - Header based)
```kubectl apply -f dynamic-routing.yaml -n demo```
- Upon inspection of the above configuration file, you will see that if the header key: value of "user-id: 1" is found, requests will be routed to target v2.1
- ```curl <ip>:<port> -H "user-id: 1"```
- Above command would always be routed to the service 2.1

### Monitoring / Observability
- For monitoring and observability, we need to port-forward the Grafana & Jager services to our local machine
- ```kubectl port-forward -n nginx-mesh --address=0.0.0.0 svc/grafana 3000:3000&```
- ```kubectl port-forward -n nginx-mesh --address=0.0.0.0 svc/jaeger 16686:16686&```   
- After that, from you local browser, access port 3000 for Grafance & 16686 for Jager. 
