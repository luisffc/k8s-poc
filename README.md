# Connected Microservices with Kubernetes - POC

## Running the Microservices

`cd` to project root, then run the following commands to install the microservices:

``` bash 
helm install service-a ./charts/microservice --set image.repository=service-a
helm install service-b ./charts/microservice --set image.repository=service-b
```

## Accessing the Microservices
To access the microservices, you can use `kubectl port-forward` to forward a local port to a port on the pod. This allows you to access the service from your local machine.

### Service B

```bash
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=service-b,app.kubernetes.io/instance=service-b" -o jsonpath="{.items[0].metadata.name}")
export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
export POD_NAME=microservice-6bf7b8c4c7-frtqd
export CONTAINER_PORT=80
echo "Visit http://127.0.0.1:8012 to use your application"
kubectl --namespace default port-forward $POD_NAME 8012:$CONTAINER_PORT
```
