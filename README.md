# learning-istio

git clone https://github.com/ahlinist/learning-istio.git  
cd learning-istio

minikube start --memory=16384 --cpus=4 --kubernetes-version=v1.14.2

*\#install istio*  
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.3.4 sh -

*\#install custom resource definitions*  
for i in istio-1.3.4/install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done

*\#install demo profile*  
kubectl apply -f install-istio.yaml

*\#verifying installation*  
kubectl get svc -n istio-system

*\#deploy app*  
kubectl label namespace default istio-injection=enabled  
kubectl create -n default -f deployment.yaml  
kubectl expose deployment hello-from-container --type=LoadBalancer --port=8080  
minikube service hello-from-container  

*\#apply custom filter to collect metrics*  
kubectl apply -f metrics.yaml  

kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090 &  
open: http://localhost:9090/graph  

custom metrics are available via the following query execution: 
- istio_requests_total
- istio_custom_request_count  
- envoy_cluster_internal_upstream_rq

*\#grafana*  
kubectl apply -f install-istio.yaml
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000 &  
goto: http://localhost:3000/dashboard/db/istio-mesh-dashboard  
