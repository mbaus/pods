# Hier möchte ich eine Entwicklungsumgebung aufbauen.
## Lade die Configuration von Github
```
cd /Volumes/LaCie/
mkdir spielwiese
cd spielwiese
gh repo clone mbaus/pods minikubeTest
cd minikubeTest/
git config pull.rebase false
```

## Starte die Minikube Umgebung
```
export MINIKUBE_HOME=/Volumes/LaCie/VMs/minikube;
# minikube start --vm-driver=parallels --cpus=8 --memory=32768 --disk-size=120000
minikube config set vm-driver parallels
minikube config set cpus 8
minikube config set memory 32768
minikube config set disk-size 307200
#minikube config set feature-gates "DynamicKubeletConfig=true"
minikube start
kubectl get all
minikube dashboard &
```

## installiere Jenkins
```
eval $(minikube docker-env)
cd jenkins-ci
docker image build -t myjenkins .
kubectl apply -f jenkins.yaml
kubectl get all -A
minikube ip
```

## Installiere PostgreSQL and PGAdmin
username: admin@dacomb.loc
password: root
```
cd ../postgres
kubectl apply -f postgres-workload.yaml
cd ../pgadmin
kubectl apply -f pgadmin-workload.yaml
kubectl get all
```

## Install prometheus and Grafana
username: admin
password: prom-operator
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts\n
helm search repo prometheus-community
kubectl create namespace monitoring
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitoring
kubectl --namespace monitoring get pods -l "release=kube-prometheus-stack"
#kubectl edit service/kube-prometheus-stack-grafana -n monitoring
kubectl patch svc kube-prometheus-stack-grafana -p '{"spec": {"type": "NodePort"}}' -n monitoring
kubectl get all -n monitoring
```

## EFK Stack
```
cd ../fluentd
kubectl apply -f .
cd ../logging
kubectl create namespace logging
kubectl apply -n logging  -f .
kubectl get all -n logging
kubectl get all
```

## Sonarqube
set up a databse with Name sonarqube
```
cd ../sonar-latest
kubectl apply -f .
```

## Rabit MQ
```
cd ../rabbit-mq
kubectl apply -f .\n
```

## zipKing
```
cd ../zipkin
kubectl apply -f .\n
```



minikube addons enable helm-tiller
minikube addons enable ingress

cd test/charts.gitlab.io/charts/gitlab-omnibus
helm dependency update
cd ../gitlab-runner
helm dependency update
cd ../gitlab-omnibus
helm dependency update

helm repo add center https://repo.chartcenter.io
helm repo update
kubectl create namespace artifactory
helm upgrade --install artifactory --set postgresql.enabled=false --namespace artifactory center/jfrog/artifactory
kubectl get svc --namespace artifactory -w artifactory-artifactory-nginx
watch -n 5 kubectl get all -n artifactory
POSTGRES_PASSWORD=$(kubectl get secret -n artifactoy -o jsonpath="{.data.postgresql-password}" | base64 --decode)
jobs
minikube dashboard &
jobs
fg %1
minikube stop
clear
ls



watch -n 5 kubectl get all


##########


export HTTP_PROXY=192.168.178.50:56362
export NO_PROXY=localhost,127.0.0.1,10.96.0.0/12,192.168.99.0/24,192.168.39.0/24
export NO_PROXY=localhost,127.0.0.1,10.211.55.0/24
