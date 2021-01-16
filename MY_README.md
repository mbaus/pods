cd spielwiese/minikubeTest
git staus
#minikube status
#minikube delete --all=true --purge=true
minikube start --vm-driver=parallels --cpus=8 --memory=8196 --disk-size=60000
kubectl get all
minikube dashboard &
eval $(minikube docker-env)
cd jenkins-ci
docker image build -t myjenkins .
kubectl apply -f jenkins.yaml
kubectl get all -A
minikube ip
cd ../postgres
kubectl apply -f postgres-workload.yaml
cd ../pgadmin
kubectl apply -f pgadmin-workload.yaml
kubectl get all
minikube ssh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts\n
helm search repo prometheus-community
kubectl create namespace monitoring
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitoring
kubectl --namespace monitoring get pods -l "release=kube-prometheus-stack"
#kubectl edit service/kube-prometheus-stack-grafana -n monitoring
kubectl patch svc kube-prometheus-stack-grafana -p '{"spec": {"type": "NodePort"}}' -n monitoring
kubectl get all -n monitoring
cd ../fluentd
kubectl apply -f .
cd ../logging
kubectl create namespace logging
kubectl apply -n logging  -f .
kubectl get all -n logging
kubectl get all
cd ../sonar-latest
kubectl apply -f .
kubectl apply -f .
#kubectl delete -n default deployment sonarqube
#kubectl delete -n default service sonarqube
#kubectl delete -n default persistentvolumeclaim conf
#kubectl delete -n default persistentvolumeclaim extensions
#kubectl delete -n default persistentvolumeclaim data
#kubectl delete -n default persistentvolumeclaim logs
#kubectl delete persistentvolume conf
#kubectl delete persistentvolume extension
#kubectl delete persistentvolume extensions
#kubectl delete persistentvolume data
#kubectl delete persistentvolume logs
#kubectl apply -f .


kubectl delete -n default deployment sonarqube
kubectl delete -n default service sonarqube
kubectl delete -n default persistentvolumeclaim pvc-conf
kubectl delete -n default persistentvolumeclaim pvc-extensions
kubectl delete -n default persistentvolumeclaim pvc-data
kubectl delete -n default persistentvolumeclaim pvc-logs
kubectl delete persistentvolume pv-conf
kubectl delete persistentvolume pv-extensions
kubectl delete persistentvolume pv-data
kubectl delete persistentvolume pv-logs
kubectl apply -f .
