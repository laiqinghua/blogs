# Istio 初体验

---

### 1. 安装使用

- 下载

```shell
curl -L https://istio.io/downloadIstio | sh -
```

- 按照文档添加PATH

```shell
export PATH="$PATH:/root/istio-1.13.4/bin"
```

- 或者添加到局部环境PATH

```shell
echo 'export PATH="$PATH:/root/istio-1.13.4/bin"' >> ~/.bashrc
```

- 默认安装

```shell
istioctl maniftest apply 
```

- 安装其他插件

```shell
cd istio-1.13.4/samples/addons
kubectl apply -f xxxxx
```

- 使用官方案例进行测试注入Sidecar 

```shell
kubectl label namespace default istio-injection=enabled
```

自动注入的原理是利用了Kubernetes Adminission Controller 这个功能结合自定义的Webhook实现的一个注入。相当于在部署应用清单时，会自动改写资源清单，将具体Sidecar的配置添加进去。

- 部署应用

```shell
 kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

- 部署应用的Ingress

```shell
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```

​	其中提供了Gateway、VirtualService

​	Gateway 提供了一个对外提供访问的入口 

​	VirtualService配合Gateway使用，配置了基本的路由信息

- 部署好了之后通过ingress发现访问不了，检查发现istio默认的ingress 类型是LoadBalancer,修改为Nodepro后解决

![image-20220524220427770](/home/lqh/blogs/img/image-20220524220427770.png)

通过ingress暴露的NodePort端口和VirtualService 提供的URL路径来进行访问。

```shell
[root@master10 addons]# kubectl get svc -n istio-system  istio-ingressgateway 
NAME                   TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)                                                                      AGE
istio-ingressgateway   NodePort   10.68.88.230   <none>        15021:32266/TCP,80:32089/TCP,443:30804/TCP,31400:31449/TCP,15443:32041/TCP   118m

[root@master10 addons]# kubectl describe virtualservices.networking.istio.io bookinfo 
  Http:
    Match:
      Uri:
        Exact:  /productpage
      Uri:
        Prefix:  /static
      Uri:
        Exact:  /login
      Uri:
        Exact:  /logout
      Uri:
        Prefix:  /api/v1/products
    Route:
      Destination:
        Host:  productpage
        Port:
          Number:  9080
          
[root@master10 addons]# curl http://192.168.0.10:32089/productpage
```

### 2. Istio设置动态路由

​	通过VirtualService 和DestionationRules 两个api资源进行动态路由设置。

- 虚拟服务 VirtualService 

  - 定义路由规则
  - 描述满足条件的请求去哪儿

- 目标规则DestionationRules

  - 定义子集、策略
  - 描述到达目标的请求怎么处理

  
