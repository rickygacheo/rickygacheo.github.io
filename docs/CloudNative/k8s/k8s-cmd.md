



```
kubectl config get-contexts
kubectl config use-context kubernetes-dev
kubectl config use-context kubernetes-dev
```

    kubectl config get-contexts
    kubectl cp fabric-ep-server/`kubectl get pods -n fabric-ep-server |awk -F " " '{print $1}'|tail -n +2`:/opt/cloud/logs/lhs-ep-business/lhs-ep-business.2024-12-03.0.zip 3.0.zip
    kubectl exec `kubectl get pods -n fabric-ep-server |awk -F " " '{print $1}'|tail -n +2` --container=fabric-ep-server --namespace=fabric-ep-server --stdin=true --tty=true -- /bin/bash


~~~
污点使用
kubectl taint nodes node1 key1=value1:NoSchedule 添加污点
kubectl taint nodes node1 key1=value1:NoSchedule- 移除污点

go mod 常用命令
go mod init  # 初始化go.mod
go mod tidy  # 更新依赖文件
go mod download  # 下载依赖文件
go mod vendor  # 将依赖转移至本地的vendor文件
go mod edit  # 手动修改依赖文件
go mod graph  # 打印依赖图
go mod verify  # 校验依赖