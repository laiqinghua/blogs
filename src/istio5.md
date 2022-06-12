# Ingress

---

- 服务的访问入口，接受外部请求并转发到后端服务
- istio的 Ingress gateway和Kubernetes ingerss的区别
  - Kubernets： 针对L7协议（资源受限），可以定义路由规则
  - istio：针对L4-6协议，之定义接入点，复用Virtual Service 的L7路由定义




- ingress 暴露入口

```yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: httpbin-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - 'qh.t.com'
    port:
      name: http
      number: 80
      protocol: HTTP
```

- virtualService url匹配路由规则

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: test-gateway
spec:
  gateways:
  - httpbin-gateway
  hosts:
  - qh.t.com
  http:
  - match:
    - uri:
        prefix: /stauts
    - uri:
        prefix: /
    route:
    - destination:
        host: httpbin
        port:
          number: 8000
```

