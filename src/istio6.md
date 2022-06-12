# 超时和重试

---

- 超时
  - 控制故障范围，避免故障扩散。
    - 当调用上游服务时一直没有响应，设置一个等待时间，达到等待时间后就直接返回超时。即调用失败。
- 重试
  - 解决网络抖动时通信失败的问题。
    - 不断的去重试调用。
- 通过在virtualService中添加超时和重试的配置项，完成：
  - 超时策略
  - 重试策略
- vs中故障注入设置超时时间， vs中设置超时失败时间

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - fault: // 故障注入超时5秒
      delay:
        fixedDelay: 5s
        percent: 100
    retries:  // 重试2次，每次等待时间
      attempts: 2
      perTryTimeout: 1s
    route:
    - destination:
        host: ratings
        subset: v1
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
    timeout: 0.5s // 超时0.5秒失败
    
```

