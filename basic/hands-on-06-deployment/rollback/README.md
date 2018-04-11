# Deployment rollback example

## 檢查 update 記錄

```sh
$ kubectl rollout history deploy nginx-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>
```

## 回到上一個版本

```sh
$ kubectl rollout undo  deploy nginx-deployment
```

## 回到版本 1 
```sh
$ kubectl rollout undo deploy nginx-deployment --to-revision=1
```