# hands-on-08-secret

## Cretae base64 content

```sh
$ echo -n "Password1234" | base64
UGFzc3dvcmQxMjM0
```

## Create secret, deploymanets, and service

```sh
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
```

## Use secret as mounted file

```sh
$ kubectl create -f mounted-secret/mounted-secret.yaml
deployment.apps "nginx-deployment" created

$ kubectl get pod
NAME                                    READY     STATUS    RESTARTS   AGE
nginx-deployment-798d7849fb-7p7w2           1/1       Running   0          8m

# read the mounted secret content
$ kubectl exec -ti nginx-deployment-798d7849fb-7p7w2 -- cat /etc/db-secret/password
Password1234
```

## Cleanup

```sh
$ kubectl delete deploy mysql-deploy
$ kubectl delete deploy nginx-deployment
$ kubectl delete deploy wordpress-deployment

$ kubectl delete svc mysql-svc
$ kubectl delete svc wordpress-svc

$ kubectl delete secret db-password

$ rm -rf /tmp/data
```