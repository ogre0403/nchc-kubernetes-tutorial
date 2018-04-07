# Rolling Update Example

## 準備新版本的nginx

```sh
$ docker build -t ogre0403/nginx:v2 .
...
Successfully built 7fcba08c1ae3
Successfully tagged ogre0403/nginx:v2


$ docker push ogre0403/nginx:v2
The push refers to repository [docker.io/ogre0403/nginx]
4bae89b74651: Pushed
2da2bf98390b: Mounted from library/nginx
6f089fbf172c: Mounted from library/nginx
3358360aedad: Mounted from library/nginx
v2: digest: sha256:22a2918008c474f8adeb2918b7c9ed74d284be50e140e486acaf511f17b85f94 size: 1155
```

## 修改 nginx-deployment 使用的 image

```sh
$ kubectl edit deployment nginx-deployment
...
    spec:
      containers:
      - image: ogre0403/nginx:v2
        imagePullPolicy: IfNotPresent
...
```

## 檢查rolling update的 進度

```sh
$ kubectl rollout status deploy  nginx-deployment
Waiting for rollout to finish: 2 out of 4 new replicas have been updated...
Waiting for rollout to finish: 2 out of 4 new replicas have been updated...
```
