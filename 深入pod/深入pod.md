# 深入Pod

## pod的配置

### 命令式配置

```shell
kubectl run nginx --image=nginx:latest --port=80 --namespace dev
```

### yaml式配置

```shell
kubectl create -f pod-nginx.yaml
```

[pod-nginx.yaml](./kubernetes_learning/yaml/深入pod/pod-nginx.yaml)

## 探针

### 类型

1. livenessProbe
    - 容器启动成功后，检查是否存活, 如果不存活，kubelet会重启容器
2. readinessProbe
    - 容器启动成功后, 准备好接受流量
3. startupProbe
    - 排他性，只有在startupProbe成功后，才会执行livenessProbe和readinessProbe

### 探测方式

1. exec
    - 执行一个命令，如果返回0，表示成功
2. http
    - 发送一个http请求，如果返回200-400，表示成功
3. tcpSocket
    - 连接一个端口，如果成功，表示成功

### 参数配置

1. initialDelaySeconds
    - 容器启动后，多久开始探测
2. periodSeconds
    - 探测间隔
3. timeoutSeconds
    - 探测超时时间
4. successThreshold：1次
    - 连续成功多少次，认为成功
5. failureThreshold：2次
    - 连续失败多少次，认为失败

### 例子
1. exec
```yaml
      startupProbe:
        exec:
          command:
            - cat
            - /tmp/healthy
        failureThreshold: 3
        periodSeconds: 10
        successThreshold: 1
        timeoutSeconds: 5
```

2. http
````yaml
      startupProbe:
        httpGet:
          path: /health
          port: 80
        failureThreshold: 3
        periodSeconds: 10
        successThreshold: 1
        timeoutSeconds: 5
````

3. tcpSocket
```yaml
      startupProbe:
        tcpSocket:
          port: 80
        failureThreshold: 3
        periodSeconds: 10
        successThreshold: 1
        timeoutSeconds: 5
```

## 生命周期
### pod退出流程
   - Endpoint删除pod ip地址(网络信息)
   - Pod变成Terminating状态
     - terminationGracePeriodSeconds: 如果超过设置时间，pod才会被删除
   - 执行preStop指令

### preStop的应用
   - 注册中心下线
   - 数据清理
   - 数据销毁




