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

*\#deploy app*  
kubectl label namespace default istio-injection=enabled  
kubectl create -n default -f deployment.yaml  
kubectl expose deployment hello-from-container --type=LoadBalancer --port=8080  
minikube service hello-from-container  

kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090 &  
open: http://localhost:9090/graph  

metrics are available via the following query execution: 
- istio_requests_total  

*\#grafana*  
kubectl apply -f install-grafana.yaml  
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000 &  
goto: http://localhost:3000/dashboard/db/istio-mesh-dashboard  
