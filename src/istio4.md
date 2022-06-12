# ServiceEntry 服务入口

---

- 添加外部服务到网格内
- 管理到外部服务的请求
- 扩展网格

## 配置说明

```yaml
apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: "name"
spec:
  hosts: #外部服务域名列表
  - "xxxx"
  - "xxxx"
  addresses: #外部服务IP
  ports: #外部服务具体端口具体协议
  - name:
    number:
    protocol: 
  endpoints: #具体的节点
  - address
    ports:
  resolution: DNS/STATIC/NONE #服务发现机制
  location: MESH_EXTERNAL/MESH_INTERNAL  #定义网格内部还是外部
    
```



## 配置案例

```yaml
apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: svc-entry
spec:
  hosts: #外部服务域名
  - "httpbin.org"
  ports:
  - name: http
    number: 80 #外部服务端口
    protocol: HTTP #外部服务协议
  resolution: DNS #服务发现机制
  location: MESH_EXTERNAL #定义网格内部还是外部 MESH_INTERNAL
```

