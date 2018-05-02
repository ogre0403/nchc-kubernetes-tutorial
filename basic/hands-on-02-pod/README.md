# Hands-on-02-Pod

## Create Pod

```sh
$ kubectl create -f one-pod-two-container.yaml
```

## port-forward

```sh
$ kubectl port-forward mysql-wordpress-pod 8080:80
```

## 透過 X-window 連線

## Cleanup

```sh
$ kubectl delete -f one-pod-two-container.yaml
```