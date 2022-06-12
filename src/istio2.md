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

  

```config
VirtualService:
spec:
  host: #设置目标地址
  gateways: #匹配设置的网关，如果是服务网格内部的vs则不需要配置该项
  http: #具体路由匹配规则
  - match: #满足何种请求来进行路由
      url:
      scheme:
      method:
      headers:
      port:
    route: # 满足了上面的条件后，会被设置的路由路由到何种位置
      destination: #指定到那个Destination资源，也就是vs和ds绑定的重要点
      weight: #路由权重
      headers: #添加头部信息
  tls: #类似http
  tcp: #类似http
  exportTo: #设置可见性，可以设置部分ns可见

DestinationRule:
  host: #最终路由到的目标地址
  subsets: #子集主要给服务指定版本、制定一些TrafficPolicy策略
    name: #dr名字  VS中的destination中的名字对应
    labels:
    trafficPolicy: #指定一些策略 
      loadBalancer:  # 修改负载均衡的负载算法
      connectionPool:  # 链接池大小
      outllerDetection: 
      tls:
      portLevelSettings:
  trafficPolicy:
  exportTo:
```

- 案例 根据HTTP请求头进行匹配，并按照版本划分请求流量
  
  ```yaml
  apiVersion: networking.istio.io/v1beta1
  kind: VirtualService
  spec:
    gateway:
    - bookinfo-gateway
    hosts:
    - reviews
    http:
    - match: # 匹配HTTP头部信息
      - url:
      - headers:
          end-user:
            regex: lqh
      route: # 匹配到后转到DR 
      - destination:
          host: reviews
          subset: v2
    - match:
      - headers:
          end-user:
            regex: qh
      route: # 按照版本划分比例，比例总和必须要能被100整除
      - destination:
          host: reviews
          subset: v2
        weight: 70
      - destination:
          host: reviews
          subset: v3
        weight: 30
    - route:  #默认路由
      - destination:
          host: reviews
          subset: v1
   
  ```
  
  
  
  
  
- 应用场景：
  
  1. 按服务版本进行路由：
  2. 按比例切分流量：
  3. 根据匹配的规律进行路由：
  4. 定义各种策略（负载均衡算法、连接池等）：

