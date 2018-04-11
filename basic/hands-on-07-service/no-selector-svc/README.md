# Services without selectors example

## 用docker 建立一個nginx，模擬位於外部的http服務

```sh
$ docker run -d --rm -p 8000:80  nginx:1.7.9
```

## 建立指到外部nginx的endpoint

```yaml
kind: Endpoints
apiVersion: v1
metadata:
  name: foreign-web-ep
subsets:
- addresses:
  - ip: 192.168.2.31
  ports:
  - port: 8000
    protocol: TCP
```

## 建立一個沒有selector的Service，讓它使用endpoint

```yaml
kind: Service
apiVersion: v1
metadata:
  name: foreign-web-ep
spec:
  ports:
  - port: 80
    targetPort: 8000  
    protocol: TCP
```