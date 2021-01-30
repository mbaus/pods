cd ~/spielwiese/minikubeTest
mkdir samples
git submodule add https://github.com/zzOzz/minikube-gitlab-tutorial.git samples/minikube-gitlab-tutorial
git submodule add https://github.com/instrumenta/kubernetes-json-schema.git samples/kubernetes-json-schema
cd kubernetes-json-schema
cd samples/kubernetes-json-schema
ls
#export KUBEVAL_SCHEMA_LOCATION=file://$(pwd)
export KUBEVAL_SCHEMA_LOCATION=file:///Users/mbaus/spielwiese/minikubeTest/samples/kubernetes-json-schema
echo $KUBEVAL_SCHEMA_LOCATION
cd ~/spielwiese/minikubeTest/samples/minikube-gitlab-tutorial/
kubeval -v 1.18.1 docker-registry/*
kubeval -v 1.17.0 docker-registry/*
