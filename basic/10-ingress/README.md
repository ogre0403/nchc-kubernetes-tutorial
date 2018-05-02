# hands-on-10-ingress

## create ingress

```sh
$ kubectl create -f ingress-controller/
```

## create mysql and wordpress 

```sh
$ kubectl create -f blog/
```

## create ingress rule and /etc/hosts

```sh
# create ingress
$ kubectl create -f ingress.yaml

# add new ip/name mapping
$ echo "10.0.2.4        blog.example.com" >> /etc/hosts

# use blog.exampe.com:<NodePort> to access wordpress
$ curl blog.exampe.com:<NodePort>
```

## create nginx web

```sh
$ kubectl create -f nginx/nginx-pod.yaml
$ kubectl create -f nginx/nginx-svc.yaml
```

## configure ingress rule and /etc/hosts

```sh
# add new ingress rule
$ kubectl edit ingress wordpress-ingress
...
  - host: nginx.example.com
    http:
      paths:
      - backend:
          serviceName: nginx-svc
          servicePort: 80

# add new ip/name mapping
$ echo "10.0.2.4        nginx.example.com" >> /etc/hosts

# use nginx.exampe.com:<NodePort> to access nginx
$ curl nginx.exampe.com:<NodePort>
```

## Cleanup

```sh
$ kubectl delete -f ingress-controller
$ kubectl delete -f nginx
$ kubectl delete -f blog
$ kubectl delete -f ingress.yaml

$ rm -rf /tmp/data
```