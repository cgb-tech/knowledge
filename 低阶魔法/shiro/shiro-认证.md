RBAC是基于角色的权限管理

简单理解:谁扮演什么角色,被允许什么操作

检查角色

```java
subject.hasRole("admin");
subject.checkRole("admin");
```

检查权限

```java
subject.isPermitted("user:*"); 
subject.checkPermission("user:*");
```

