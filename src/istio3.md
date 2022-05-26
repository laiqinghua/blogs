# Gateway

---

## 功能

- 暴露网格内服务给外界访问
- 访问安全（HTTPS、mTLS等）
- 统一应用入口，API聚合

## 配置说明

- gateway配置文件

```yaml
Gateway
  servers:#定义入口点
  	port:
  	  number: #对外暴露端口
  	  protocol: #对外暴露协议
  	  name:
  	hosts:
  	tls:
  	defaultEndpoint:
  selector:#选择现有网关的标签
  	
```

- 案例

```yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: t-gateway
  namespace: default
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - '*'
    port:
      name: http
      number: 80
      protocol: HTTP
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: test-gateway
spec:
  hosts:
  - "*"
  gateways:
  - t-gateway
  http:
  - match:
    - uri:
        prefix: /details
    - uri:
        exact: /health
    route:
    - destination:
        host: details
        port:
          number: 9080
```

