# Pod安全设置（Security Context）

---

​	Kubernetes可以为Pod设置应用程序运行所需的权限或者访问控制等安全设置，设计多种LInux Kernel安全相关的系统参数，这些安全设置被称为Security Context，在Pod或Container级别通过SecurityContext字段进行设置（如果Pod和Container级别都设置了相同的安全字段，则容器将使用Container级别的设置）。

- 可以在集群维度设置PodSecurityPolicy策略会对Pod的SecurityContext安全设置进行校验，对于不满足PodSecurityPolicy策略的Pod，系统将禁止创建。（可以查书第五版本421页）

- tttttt

  