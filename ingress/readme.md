# Test

```
kubectl get ingress
kubectl create deployment web --image=gcr.io/google-samples/hello-app:1.0
kubectl expose deployment web --type=NodePort --port=8080
kubectl get service web
minikube service web --url
minikube ip
curl $(minikube service web --url)

kubectl apply -f example-ingress.yaml

kubectl get ingress

cat /etc/hosts
sudo vim /etc/hosts

curl hello-world.info

kubectl create deployment web2 --image=gcr.io/google-samples/hello-app:2.0
kubectl expose deployment web2 --port=8080 --type=NodePort\n
kubectl apply -f example-ingress.yaml

curl hello-world.info
curl hello-world.info/v2

```
