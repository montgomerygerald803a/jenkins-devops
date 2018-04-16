# Minikube

Deploy Jenkins to k8s Minikube cluster.

## Deploy Jenkins to Minikube

```bash
# create cluster
minikube start

kubectl config use-context minikube

# install Istio 0.7.1 without mTLS
kubectl apply -f $ISTIO_HOME/install/kubernetes/istio.yaml

# deploy v2 to local minikube dev environment
sh ./part1-create-environment.sh
sh ./part2-deploy-v2-dev.sh

kubectl get pods -n devops

# get new password
kubectl exec -it jenkins-devops-f57bc8c55-7542r -n devops \
  cat /var/jenkins_home/secrets/initialAdminPassword

# install NodeJS and thinBackup plugin

# copy backup files from container to local drive
kubectl cp \
  devops/jenkins-devops-f57bc8c55-7542r:/tmp/FULL-2018-04-16_02-00 \
  FULL-2018-04-16_02-0/

# discover URL and port for to connect to v2
# https://istio.io/docs/guides/bookinfo.html
# adjust for minikube ip
export GATEWAY_URL=$(minikube ip):$(kubectl get svc istio-ingress -n istio-system -o 'jsonpath={.spec.ports[0].nodePort}')
echo $GATEWAY_URL

# smoke test
curl $GATEWAY_URL

# kubernetes dashboard
minikube dashboard
```

## Misc. Commands

```bash
brew cask upgrade minikube

minikube version
minikube status
minikube dashboard

minikube get-k8s-versions

eval $(minikube docker-env)
docker ps

kubectl config use-context minikube
kubectl get nodes
kubectl get namespaces
```