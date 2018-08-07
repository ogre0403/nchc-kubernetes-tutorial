# hands-on-07-service

## Create deployments and Services

```sh
# create mysql deployment
$ kubectl create -f mysql-deployment.yaml
deployment.apps "mysql-deploy" created

# create mysql service
$ kubectl create -f mysql-service.yaml
service "mysql-svc" created

# create wordpress deployment
$ kubectl create -f wordpress-deployment.yaml
deployment.apps "wordpress-deployment" created

# create wordpress service
$ kubectl create -f wordpress-service-clusterIP.yaml
service "wordpress-svc" created

# we can use 10.107.117.75:7070 to access wordpress inside cluster
$ kubectl get svc
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                              AGE
kubernetes        ClusterIP   10.96.0.1        <none>        443/TCP                              23h
mysql-svc         ClusterIP   10.101.128.8     <none>        3306/TCP                             12m
wordpress-svc     ClusterIP   10.107.117.75    <none>        7070/TCP
```

## Create NodePort Servcie

```sh
$ kubectl create -f wordpress-service-NodePort.yaml

# We can use 10.0.2.4:30292 to access wordpress in outside cluster
$ kubectl get svc
NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                              AGE
kubernetes               ClusterIP   10.96.0.1        <none>        443/TCP                              23h
mysql-svc                ClusterIP   10.101.128.8     <none>        3306/TCP                             14m
wordpress-nodeport-svc   NodePort    10.107.30.224    <none>        7070:30292/TCP                       4s
wordpress-svc            ClusterIP   10.107.117.75    <none>        7070/TCP
```

## HostNetowrk and HostPort

Map container port 80 to host port 8088

```sh
$ kubectl create -f hostnetwork/hostport.yaml
$ curl 127.0.0.1:8088
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

Access port 80

```sh
$ kubectl create -f hostnetwork/hostnetwork.yaml
curl 127.0.0.1
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

## Services without selectors example

```sh
# 用 docker 建立一個nginx，模擬位於外部的http服務
$ docker run --name nginx179 -d --rm -p 8000:80  nginx:1.7.9

$ curl 10.0.2.4:8000
<!DOCTYPE html>
<html>
...
</html>

# 建立指到外部nginx的endpoint
$ kubectl create -f no-selector-svc/endpoint.yaml

# 建立一個沒有selector的Service，讓它使用endpoint
$ kubectl create -f no-selector-svc/service-ep.yaml

$ kubectl get svc
NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                              AGE
foreign-web-ep           ClusterIP   10.108.226.244   <none>        80/TCP                               5s
...

$ curl 10.108.226.244
<!DOCTYPE html>
<html>
...
</html>
```

## Cleanup

```sh
$ kubectl delete deploy mysql-deploy
$ kubectl delete deploy wordpress-deployment
$ kubectl delete svc foreign-web-ep
$ kubectl delete svc mysql-svc
$ kubectl delete svc wordpress-nodeport-svc
$ kubectl delete svc wordpress-svc
$ kubectl delete -f hostnetwork

$ rm -rf /tmp/data/
$ docker stop nginx179
```