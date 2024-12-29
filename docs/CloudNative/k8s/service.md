---
layout: default
title: Service
nav_order: 4
parent: k8s
permalink: /docs/CloudNative/k8s/service
---
## Service问答式学习
### Service是什么？
Service是k8s的一种资源，将运行在一个或一组 Pod 上的网络应用程序公开为网络服务的方法。其核心职责是提供Pod的访问方式

### 如何定义Service
比如下方代码，定义了一个名为my-service的Service：
    这个Service默认会以ClusterIP的访问方式提供Pod的访问，并且Service侦听在80端口，Service所转发的端口在9376，通过selector选择标签为
    MyApp的Pods
~~~
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 9376
~~~
Service具备的能力：
1、Service的Controller要能感知所要转发的后端实例状态
2、Service可以提供多个端口
3、Service也可以不指定选择标签，手动配置后端IP端口的能力，例如：
~~~
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: my-service-1 # 按惯例将 Service 的名称用作 EndpointSlice 名称的前缀
  labels:
    # 你应设置 "kubernetes.io/service-name" 标签。
    # 设置其值以匹配 Service 的名称
    kubernetes.io/service-name: my-service
addressType: IPv4
ports:
  - name: '' # 应与上面定义的 Service 端口的名称匹配
    appProtocol: http
    protocol: TCP
    port: 9376
endpoints:  # 此列表中的 IP 地址可以按任何顺序显示
  - addresses:
      - "10.4.5.6"
  - addresses:
      - "10.1.2.3"
~~~
### 那么Service除了ClusterIP，还有哪些type
#### ClusterIP
ClusterIP的方式为默认方式，此ip只能在集群内部访问。可以通过Ingress或者Gateway对外提供服务
#### NodePort
