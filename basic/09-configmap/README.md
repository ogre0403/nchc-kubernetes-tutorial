# hands-on-09-configmap

## Create configmap, secret, deployemnts, services

```sh
$ kubectl create -f mysql-cm.yaml
configmap "mysql-conf" created

$ kubectl create -f secret.yaml
secret "db-password" created

$ kubectl create -f mysql-deployment.yaml
deployment.apps "mysql-deploy" created

$ kubectl create -f mysql-service.yaml
service "mysql-svc" created

$ kubectl create -f wordpress-deployment.yaml
deployment.apps "wordpress-deployment" created

$ kubectl create -f wordpress-service.yaml
service "wordpress-svc" created

# confirm mysqld is listen on port 3360
$ kubectl logs mysql-deploy-76f7496cd-7p9cs
...
2018-05-02 07:43:01 1 [Note] Server hostname (bind-address): '*'; port: 3360
2018-05-02 07:43:01 1 [Note] IPv6 is available.
2018-05-02 07:43:01 1 [Note]   - '::' resolves to '::';
2018-05-02 07:43:01 1 [Note] Server socket created on IP: '::'.
```

## Use configmap as environment

```sh
$ kubectl create -f env-cm/env-cm.yaml
deployment.apps "nginx-deployment" created

# read env variable
$ kubectl exec -ti nginx-deployment-56d5458f4d-qx9vv env |grep MYSQL_PORT
MYSQL_PORT=3360
```

## Cleanup

```sh
$ kubectl delete deploy mysql-deploy
$ kubectl delete deploy nginx-deployment
$ kubectl delete deploy wordpress-deployment

$ kubectl delete svc mysql-svc
$ kubectl delete svc wordpress-svc

$ kubectl delete secret db-password
$ kubectl delete cm mysql-conf

$ rm -rf /tmp/data
```