# helmDockerCoins
Deploy docker coins with helm


[**作业要求**](https://github.com/qinrui777/helmDockerCoins/blob/master/101-exam.md)

### 1. create namespace
```bash
kubectl create namespace dev
kubectl create namespace uat
kubectl create namespace prod
```

### 2.在k8s环境上部署dev,uat环境redis
```bash
kubectl apply -f /vagrant/apps/kubercoins/redis-deployment.yaml -n dev
kubectl apply -f /vagrant/apps/kubercoins/redis-service.yaml -n dev

kubectl apply -f /vagrant/apps/kubercoins/redis-deployment.yaml -n uat
kubectl apply -f /vagrant/apps/kubercoins/redis-service.yaml -n uat
```

### 3. 用helm部署dev,uat环境
- 调试（部署前）  
`helm install ./coins-dev-uat --set environment=dev --dry-run --debug | less`

- 部署
```bash
# dev
helm install ./coins-dev-uat --name coins1 --namespace dev --set environment=dev
# uat
helm install ./coins-dev-uat --name coins2 --namespace uat --set environment=uat
```

- 删除release（部署后）  
`helm delete --purge coinsX`


查看资源
```bash
root@master01:/vagrant/apps# kubectl get pod,svc,ingress -n dev
NAME                          READY   STATUS    RESTARTS   AGE
pod/hasher-765489b94f-nnggc   1/1     Running   0          66m
pod/redis-686d55dddd-dhg79    1/1     Running   0          23h
pod/rng-6nw2r                 1/1     Running   0          66m
pod/webui-74fcbfdb67-nxjth    1/1     Running   0          66m
pod/worker-578b58fbfc-cpvhs   1/1     Running   0          66m

NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/hasher   ClusterIP   10.99.157.89     <none>        80/TCP         66m
service/redis    ClusterIP   10.105.245.228   <none>        6379/TCP       23h
service/rng      ClusterIP   10.106.217.109   <none>        80/TCP         66m
service/webui    NodePort    10.103.113.244   <none>        80:31713/TCP   66m
service/worker   ClusterIP   10.106.91.141    <none>        80/TCP         66m

NAME                       HOSTS                             ADDRESS   PORTS   AGE
ingress.extensions/webui   dev-webui.192.168.33.101.nip.io             80      66m
```

浏览器访问：  
- dev http://dev-webui.192.168.33.101.nip.io/index.html
- uat http://uat-webui.192.168.33.101.nip.io/index.html

### 4.部署prod


```bash
在node01上执行  
docker run --name prod-redis -d -p 6379:6379 redis:latest
```

- 部署
`helm install ./coins-prod --name coins3 --namespace prod`


浏览器访问：
- prod http://webui.192.168.33.101.nip.io/index.html
