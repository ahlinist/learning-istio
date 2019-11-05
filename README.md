# learning-istio

cd learning-istio

minikube start --memory=16384 --cpus=4 --kubernetes-version=v1.14.2

\#install istio

curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.3.4 sh -

\#install custom resource definitions

for i in istio-1.3.4/install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done

\#install demo profile

kubectl apply -f istio-1.3.4/install/kubernetes/istio-demo.yaml

\#verifying installation

kubectl get svc -n istio-system

\#deploy app

kubectl label namespace default istio-injection=enabled
kubectl create -n default -f deployment.yaml
kubectl expose deployment hello-from-container --type=LoadBalancer --port=8080
minikube service hello-from-container 
