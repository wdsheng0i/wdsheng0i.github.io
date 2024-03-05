---
layout: post
title: PLG(Promtail + Loki + Grafana) 
category: dev-ops
tags: [dev-ops]
---

日志平台：PLG(Promtail + Loki + Grafana)

## 参考资料
- Loki: https://grafana.com/docs/loki/latest/installation/
  - https://grafana.com/docs/loki/latest/configuration/
- Promtail：https://grafana.com/docs/loki/latest/clients/promtail/installation/
- Grafana：https://grafana.com/docs/grafana/latest/setup-grafana/installation/


- 配置说明：https://www.mianshigee.com/tutorial/loki/readme.md
- Grafana+Loki+Promtail 搭建日志收集系统:https://blog.csdn.net/jilo88/article/details/131241264
- PLG（Promtail + Loki + Grafana）日志系统生产快速实践:https://blog.csdn.net/AndCo/article/details/128949093
- Kubernetes Loki安装部署并收集日志：https://blog.51cto.com/u_14035463/5584882
- 轻量级 k8s 应用日志收集方案 loki：https://www.jianshu.com/p/674470fd05a7
- K8S使用LOKI实现日志收集: https://blog.csdn.net/weixin_49343462/article/details/125683145

## 前言 
Loki索引是由标签建立的,原始日志消息不被索引，采用计算换空间的方式，  
Loki特点：通过对日志索引，高效使用内存  
多租户：读写链接的多租户实现   
可扩展性: 可使用all in one 部署，即所有组件均在同一进程内; 每一个loki组件都能以微服务形式运行，配置允许单独扩展微服务，允许灵活的大规模安装。  
易用性：拥有大量的可观测性的客户端插件支持，例如支持promtail、fluentd等  
Loki与Grafana无缝集成，提供了一个完整的可观察性堆栈。  

## 一、PLG日志系统介绍
- Promtail： 日志采集器，安装部署在需要收集和分析日志的业务服务器，promtail会将日志发给Loki服务。
- Loki： 主服务器，负责存储日志和处理查询。由Grafana提供的开源日志聚合工具，一个高效存储日志数据的数据存储
- Grafana：提供web管理界面，数据搜索展示功能。

![](https://wdsheng0i.github.io/assets/images/2021/devops/loki.png)  
- promtail收集并将日志发送给loki的 Distributor 组件
- Distributor会对接收到的日志流进行正确性校验，并将验证后的日志分批并行发送到Ingester
- Ingester 接受日志流并构建数据块，压缩后存放到所连接的存储后端
- Querier 收到HTTP查询请求，并将请求发送至Ingester 用以获取内存数据 ，Ingester 收到请求后返回符合条件的数据 ；
如果 Ingester 没有返回数据，Querier 会从后端存储加载数据并遍历去重执行查询 ，通过HTTP返回查询结果

## 二、二进制jar包离线部署LPG
loki、promtail 离线包下载（选择同一版本下）：https://github.com/grafana/loki/releases/  
Grafana 下载地址：https://grafana.com/grafana/download?platform=linux  

### 2.1 loki部署 
``` 
# 1、下载zip、解压
curl -O -L "https://github.com/grafana/loki/releases/download/v2.8.4/loki-linux-amd64.zip"
或者
wget https://github.com/grafana/loki/releases/download/v2.7.1/loki-linux-amd64.zip
unzip "loki-linux-amd64.zip"
chmod a+x "loki-linux-amd64"

# 2、下载配置loki-config.yaml
wget https://raw.githubusercontent.com/grafana/loki/master/cmd/loki/loki-local-config.yaml

# 3、修改配置loki-config.yaml
auth_enabled: false
server:
  http_listen_port: 3100  # http 端口
  grpc_listen_port: 9096
common:
  path_prefix: /data/loki
  storage:
    filesystem:  # loki 存储位置
      chunks_directory: /data/loki/chunks
      rules_directory: /data/loki/rules
  replication_factor: 1
  ring:
    instance_addr: 127.0.0.1
    kvstore:
      store: inmemory
query_range:
  results_cache:
    cache:
      embedded_cache:
        enabled: true
        max_size_mb: 100
compactor:
  working_directory: /data/loki/compactor      # 压缩目录，一般也作为持久化目录
  compaction_interval: 10m                 # 压缩间隔
  retention_enabled: true                  # 持久化开启
  retention_delete_delay: 2h               # 过期后多久删除
  retention_delete_worker_count: 150       # 过期删除协程数目
schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h  
ruler:
  alertmanager_url: http://localhost:9093
limits_config:
  reject_old_samples: true   # 是否拒绝旧样本
  reject_old_samples_max_age: 168h   # 168小时之前的样本被拒绝
chunk_store_config:
  max_look_back_period: 168h  # 为避免查询超过保留期的数据，必须小于或等于下方的时间值
table_manager:
  retention_deletes_enabled: true   # 保留删除开启
  retention_period: 168h  # 超过168h的块数据将被删除，必须为24h的倍数

# 4、后台启动nohup
nohup ./loki-linux-amd64 -config.file=loki-local-config.yaml &
nohup ./loki-linux-amd64 -config.file=loki-local-config.yaml > /opt/logs/loki-3100.log 2>&1 &
如需debug日志，使用以下启动命令：
nohup ./loki-linux-amd64 -config.file=loki-local-config.yaml --log.level=debug > /opt/logs/loki-3100.log 2>&1 &

# 或者配置服务启动
cat > /etc/systemd/system/loki.service <<EOF
[Unit]
Description=loki
After=network.target 

[Service]
ExecStart=/opt/loki/loki-linux-amd64 \
	-config.file=/opt/loki/loki-local-config.yaml &>> /opt/logs/loki-3100.log
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF 

# 启动服务，设置开机自启，并检查服务开启状态
systemctl daemon-reload
systemctl start loki
systemctl status loki
systemctl enable loki
```

### 2.2 promtail部署
``` 
# 1、下载zip、解压
curl -O -L "https://github.com/grafana/loki/releases/download/v2.8.4/promtail-linux-amd64.zip"
或者
wget https://github.com/grafana/loki/releases/download/v2.2.1/promtail-linux-amd64.zip
unzip "promtail-linux-amd64.zip"
chmod a+x "promtail-linux-amd64"

# 2、下载配置loki-config.yaml
wget https://raw.githubusercontent.com/grafana/loki/main/clients/cmd/promtail/promtail-local-config.yaml

# 3、修改配置promtail-local-config.yaml
Promtail 配置文件说明：https://blog.csdn.net/JineD/article/details/127092849
server:
  http_listen_port: 9080
  grpc_listen_port: 0
positions:
  filename: /tmp/positions.yaml
clients:
  - url: http://10.0.0.32:3100/loki/api/v1/push  # 推送到loki地址 
scrape_configs:
- job_name: logs  # 唯一
  static_configs:
  - targets:
      - localhost  # 读取本地文件
    labels: # labels 用户loki labels查询
      job: admin-service
      __path__: /data/logs/admin-service/*/*info.log  # 读取后缀为info.log的日志文件
  - targets:
      - localhost  
    labels: 
      job: gateway-service
      __path__: /data/logs/gateway-service/*/*info.log 

# 4、后台启动nohup
nohup ./promtail-linux-amd64 -config.file=promtail-config.yaml &
nohup ./promtail-linux-amd64 -config.file=promtail-local-config.yaml > /opt/logs/promtail-9080.log 2>&1 &

# 或者配置系统服务启动
# cat > /etc/systemd/system/promtail.service <<EOF
[Unit]
Description=promtail
After=network.target 

[Service]
ExecStart=/opt/loki/promtail-linux-amd64 \
	-config.file=/opt/loki/promtail-local-config.yaml &>> /opt/logs/promtail-9080.log
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 启动服务，设置开机自启，并检查服务开启状态
 systemctl daemon-reload
 systemctl start promtail
 systemctl status promtail
 systemctl enable promtail
```

### 2.3 grafana部署 
1.通过rpm安装
```
1).下载安装
sudo yum install -y https://dl.grafana.com/enterprise/release/grafana-enterprise-10.0.0-1.x86_64.rpm
或者
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-10.0.0-1.x86_64.rpm
sudo yum localinstall grafana-4.6.1-1.x86_64.rpm

2).启动
service grafana-server start
加入开机启动
/sbin/chkconfig --add grafana-server

3)、配置 
修改端口，如果我们是通过yum 方式按照rpm包的话，此时可以通过 
vi  usr/share/grafana/conf/defaults.ini
对于其他文件如下
# init.d脚本
/etc/init.d/grafana-server
# 安装默认环境变量文件
/etc/sysconfig/grafana-server
# 配置文件，修改这个文件不起作用。
/etc/grafana/grafana.ini
# 日志文件
/var/log/grafana/grafana.log
# 默认sqlite3数据库 
```

2.通过tar包安装 (推荐)
``` 
1)、下载解压
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-10.0.0.linux-amd64.tar.gz
tar -zxvf grafana-enterprise-10.0.0.linux-amd64.tar.gz

2)、启动 
后台启动
nohup ./bin/grafana-server > /opt/logs/grafana.log 2>&1 &

添加启动服务
vim /usr/lib/systemd/system/grafana-server.service

[Unit]
Description=Grafana
After=network.target

[Service]
Type=notify
ExecStart=/opt/grafana/bin/grafana-server -homepath /opt/grafana
Restart=on-failure

[Install]
WantedBy=multi-user.target 
```

- gtafana：http://ip:3000/ 默认端口3000，默认账号 admin/admin, 首次登录修改密码
- loki：http://ip:3100/ grafana中配置datasource，添加loki地址即可

## 三、k8s集群部署日志平台LPG
K8S使用LOKI实现日志收集：https://blog.csdn.net/weixin_49343462/article/details/125683145

### 3.1.依次启动yaml
loki-ns.yaml
```
apiVersion: v1
kind: Namespace
metadata:
  name: logging

```

loki-rbac.yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: loki
  namespace: logging
 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: loki
  namespace: logging
rules:
- apiGroups:
  - extensions
  resourceNames:
  - loki
  resources:
  - podsecuritypolicies
  verbs:
  - use
 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: loki
  namespace: logging
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: loki
subjects:
- kind: ServiceAccount
  name: loki

```

loki-configmap.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: loki
  namespace: logging
  labels:
    app: loki
data:
  loki.yaml: |
    auth_enabled: false
    ingester:
      chunk_idle_period: 3m      # 如果块没有达到最大的块大小，那么在刷新之前，块应该在内存中不更新多长时间
      chunk_block_size: 262144
      chunk_retain_period: 1m      # 块刷新后应该在内存中保留多长时间
      max_transfer_retries: 0      # Number of times to try and transfer chunks when leaving before falling back to flushing to the store. Zero = no transfers are done.
      lifecycler:       #配置ingester的生命周期，以及在哪里注册以进行发现
        ring:
          kvstore:
            store: inmemory      # 用于ring的后端存储，支持consul、etcd、inmemory
          replication_factor: 1      # 写入和读取的ingesters数量，至少为1（为了冗余和弹性，默认情况下为3）
    limits_config:
      enforce_metric_name: false
      reject_old_samples: true      # 旧样品是否会被拒绝
      reject_old_samples_max_age: 168h      # 拒绝旧样本的最大时限
    schema_config:      # 配置从特定时间段开始应该使用哪些索引模式
      configs:
      - from: 2020-10-24      # 创建索引的日期。如果这是唯一的schema_config，则使用过去的日期，否则使用希望切换模式时的日期
        store: boltdb-shipper      # 索引使用哪个存储，如：cassandra, bigtable, dynamodb，或boltdb
        object_store: filesystem      # 用于块的存储，如：gcs, s3， inmemory, filesystem, cassandra，如果省略，默认值与store相同
        schema: v11
        index:      # 配置如何更新和存储索引
          prefix: index_      # 所有周期表的前缀
          period: 24h      # 表周期
    server:
      http_listen_port: 3100
    storage_config:      # 为索引和块配置一个或多个存储
      boltdb_shipper:
        active_index_directory: /data/loki/boltdb-shipper-active
        cache_location: /data/loki/boltdb-shipper-cache
        cache_ttl: 24h         
        shared_store: filesystem
      filesystem:
        directory: /data/loki/chunks
    chunk_store_config:      # 配置如何缓存块，以及在将它们保存到存储之前等待多长时间
      max_look_back_period: 0s      #限制查询数据的时间，默认是禁用的，这个值应该小于或等于table_manager.retention_period中的值
    table_manager:
      retention_deletes_enabled: true      # 日志保留周期开关，用于表保留删除
      retention_period: 48h       # 日志保留周期，保留期必须是索引/块的倍数
    compactor:
      working_directory: /data/loki/boltdb-shipper-compactor
      shared_store: filesystem

```

loki.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: loki
  namespace: logging
  labels:
    app: loki
spec:
  type: ClusterIP
  ports:
    - port: 3100
      protocol: TCP
      name: http-metrics
      targetPort: http-metrics
  selector:
    app: loki

---
apiVersion: v1
kind: Service
metadata:
  name: loki-outer
  namespace: logging
  labels:
    app: loki
spec:
  type: NodePort
  ports:
    - port: 3100
      protocol: TCP
      name: http-metrics
      targetPort: http-metrics
      nodePort: 32537
  selector:
    app: loki


---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: loki
  namespace: logging
  labels:
    app: loki
spec:
  podManagementPolicy: OrderedReady
  replicas: 1
  selector:
    matchLabels:
      app: loki
  serviceName: loki
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: loki
    spec:
      serviceAccountName: loki
      initContainers: []
      containers:
        - name: loki
          image: grafana/loki:2.3.0
          imagePullPolicy: IfNotPresent
          args:
            - -config.file=/etc/loki/loki.yaml
          volumeMounts:
            - name: config
              mountPath: /etc/loki
            - name: storage
              mountPath: /data
          ports:
            - name: http-metrics
              containerPort: 3100
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /ready
              port: http-metrics
              scheme: HTTP
            initialDelaySeconds: 45
            timeoutSeconds: 1
            periodSeconds: 10
            successThreshold: 1
            failureThreshold: 3
          readinessProbe:
            httpGet:
              path: /ready
              port: http-metrics
              scheme: HTTP
            initialDelaySeconds: 45
            timeoutSeconds: 1
            periodSeconds: 10
            successThreshold: 1
            failureThreshold: 3
          securityContext:
            readOnlyRootFilesystem: true
      terminationGracePeriodSeconds: 4800
      volumes:
        - name: config
          configMap:
            defaultMode: 420
            name: loki
        - emptyDir: {}
          name: storage

```

loki-promtail-configmap.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: loki-promtail
  namespace: logging
  labels:
    app: promtail
data:
  promtail.yaml: |
    client:      # 配置Promtail如何连接到Loki的实例
      backoff_config:      # 配置当请求失败时如何重试请求给Loki
        max_period: 5m 
        max_retries: 10
        min_period: 500ms
      batchsize: 1048576      # 发送给Loki的最大批次大小(以字节为单位)
      batchwait: 1s      # 发送批处理前等待的最大时间（即使批次大小未达到最大值）
      external_labels: {}      # 所有发送给Loki的日志添加静态标签
      timeout: 10s      # 等待服务器响应请求的最大时间
    positions:
      filename: /run/promtail/positions.yaml
    server:
      http_listen_port: 3101
    target_config:
      sync_period: 10s
    scrape_configs:
    - job_name: kubernetes-pods-name
      pipeline_stages:
        - docker: {}
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      - source_labels:
        - __meta_kubernetes_pod_label_name
        target_label: __service__
      - source_labels:
        - __meta_kubernetes_pod_node_name
        target_label: __host__
      - action: drop
        regex: ''
        source_labels:
        - __service__
      - action: labelmap
        regex: __meta_kubernetes_pod_label_(.+)
      - action: replace
        replacement: $1
        separator: /
        source_labels:
        - __meta_kubernetes_namespace
        - __service__
        target_label: job
      - action: replace
        source_labels:
        - __meta_kubernetes_namespace
        target_label: namespace
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_name
        target_label: pod
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_container_name
        target_label: container
      - replacement: /var/log/pods/*$1/*.log
        separator: /
        source_labels:
        - __meta_kubernetes_pod_uid
        - __meta_kubernetes_pod_container_name
        target_label: __path__
    - job_name: kubernetes-pods-app
      pipeline_stages:
        - docker: {}
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      - action: drop
        regex: .+
        source_labels:
        - __meta_kubernetes_pod_label_name
      - source_labels:
        - __meta_kubernetes_pod_label_app
        target_label: __service__
      - source_labels:
        - __meta_kubernetes_pod_node_name
        target_label: __host__
      - action: drop
        regex: ''
        source_labels:
        - __service__
      - action: labelmap
        regex: __meta_kubernetes_pod_label_(.+)
      - action: replace
        replacement: $1
        separator: /
        source_labels:
        - __meta_kubernetes_namespace
        - __service__
        target_label: job
      - action: replace
        source_labels:
        - __meta_kubernetes_namespace
        target_label: namespace
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_name
        target_label: pod
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_container_name
        target_label: container
      - replacement: /var/log/pods/*$1/*.log
        separator: /
        source_labels:
        - __meta_kubernetes_pod_uid
        - __meta_kubernetes_pod_container_name
        target_label: __path__
    - job_name: kubernetes-pods-direct-controllers
      pipeline_stages:
        - docker: {}
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      - action: drop
        regex: .+
        separator: ''
        source_labels:
        - __meta_kubernetes_pod_label_name
        - __meta_kubernetes_pod_label_app
      - action: drop
        regex: '[0-9a-z-.]+-[0-9a-f]{8,10}'
        source_labels:
        - __meta_kubernetes_pod_controller_name
      - source_labels:
        - __meta_kubernetes_pod_controller_name
        target_label: __service__
      - source_labels:
        - __meta_kubernetes_pod_node_name
        target_label: __host__
      - action: drop
        regex: ''
        source_labels:
        - __service__
      - action: labelmap
        regex: __meta_kubernetes_pod_label_(.+)
      - action: replace
        replacement: $1
        separator: /
        source_labels:
        - __meta_kubernetes_namespace
        - __service__
        target_label: job
      - action: replace
        source_labels:
        - __meta_kubernetes_namespace
        target_label: namespace
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_name
        target_label: pod
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_container_name
        target_label: container
      - replacement: /var/log/pods/*$1/*.log
        separator: /
        source_labels:
        - __meta_kubernetes_pod_uid
        - __meta_kubernetes_pod_container_name
        target_label: __path__
    - job_name: kubernetes-pods-indirect-controller
      pipeline_stages:
        - docker: {}
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      - action: drop
        regex: .+
        separator: ''
        source_labels:
        - __meta_kubernetes_pod_label_name
        - __meta_kubernetes_pod_label_app
      - action: keep
        regex: '[0-9a-z-.]+-[0-9a-f]{8,10}'
        source_labels:
        - __meta_kubernetes_pod_controller_name
      - action: replace
        regex: '([0-9a-z-.]+)-[0-9a-f]{8,10}'
        source_labels:
        - __meta_kubernetes_pod_controller_name
        target_label: __service__
      - source_labels:
        - __meta_kubernetes_pod_node_name
        target_label: __host__
      - action: drop
        regex: ''
        source_labels:
        - __service__
      - action: labelmap
        regex: __meta_kubernetes_pod_label_(.+)
      - action: replace
        replacement: $1
        separator: /
        source_labels:
        - __meta_kubernetes_namespace
        - __service__
        target_label: job
      - action: replace
        source_labels:
        - __meta_kubernetes_namespace
        target_label: namespace
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_name
        target_label: pod
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_container_name
        target_label: container
      - replacement: /var/log/pods/*$1/*.log
        separator: /
        source_labels:
        - __meta_kubernetes_pod_uid
        - __meta_kubernetes_pod_container_name
        target_label: __path__
    - job_name: kubernetes-pods-static
      pipeline_stages:
        - docker: {}
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      - action: drop
        regex: ''
        source_labels:
        - __meta_kubernetes_pod_annotation_kubernetes_io_config_mirror
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_label_component
        target_label: __service__
      - source_labels:
        - __meta_kubernetes_pod_node_name
        target_label: __host__
      - action: drop
        regex: ''
        source_labels:
        - __service__
      - action: labelmap
        regex: __meta_kubernetes_pod_label_(.+)
      - action: replace
        replacement: $1
        separator: /
        source_labels:
        - __meta_kubernetes_namespace
        - __service__
        target_label: job
      - action: replace
        source_labels:
        - __meta_kubernetes_namespace
        target_label: namespace
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_name
        target_label: pod
      - action: replace
        source_labels:
        - __meta_kubernetes_pod_container_name
        target_label: container
      - replacement: /var/log/pods/*$1/*.log
        separator: /
        source_labels:
        - __meta_kubernetes_pod_annotation_kubernetes_io_config_mirror
        - __meta_kubernetes_pod_container_name
        target_label: __path__

```

loki-promtail-rbac.yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: loki-promtail
  labels:
    app: promtail
  namespace: logging
 
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  labels:
    app: promtail
  name: promtail-clusterrole
  namespace: logging
rules:
- apiGroups: [""] # "" indicates the core API group
  resources:
  - nodes
  - nodes/proxy
  - services
  - endpoints
  - pods
  verbs: ["get", "watch", "list"]
 
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: promtail-clusterrolebinding
  labels:
    app: promtail
  namespace: logging
subjects:
  - kind: ServiceAccount
    name: loki-promtail
    namespace: logging
roleRef:
  kind: ClusterRole
  name: promtail-clusterrole
  apiGroup: rbac.authorization.k8s.io

```

loki-promtail.yaml
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: loki-promtail
  namespace: logging
  labels:
    app: promtail
spec:
  selector:
    matchLabels:
      app: promtail
  updateStrategy:
    rollingUpdate:
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: promtail
    spec:
      serviceAccountName: loki-promtail
      containers:
        - name: promtail
          image: grafana/promtail:2.3.0
          imagePullPolicy: IfNotPresent
          args:
          - -config.file=/etc/promtail/promtail.yaml
          - -client.url=http://loki:3100/loki/api/v1/push
          env:
          - name: HOSTNAME
            valueFrom:
              fieldRef:
                apiVersion: v1
                fieldPath: spec.nodeName
          volumeMounts:
          - mountPath: /etc/promtail
            name: config
          - mountPath: /run/promtail
            name: run
          - mountPath: /var/lib/docker/containers
            name: docker
            readOnly: true
          - mountPath: /var/log/pods
            name: pods
            readOnly: true
          ports:
          - containerPort: 3101
            name: http-metrics
            protocol: TCP
          securityContext:
            readOnlyRootFilesystem: true
            runAsGroup: 0
            runAsUser: 0
          readinessProbe:
            failureThreshold: 5
            httpGet:
              path: /ready
              port: http-metrics
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
      tolerations:
      - effect: NoSchedule
        key: node-role.kubernetes.io/master
        operator: Exists
      volumes:
        - name: config
          configMap:
            defaultMode: 420
            name: loki-promtail
        - name: run
          hostPath:
            path: /run/promtail
            type: ""
        - name: docker
          hostPath:
            path: /var/lib/docker/containers
        - name: pods
          hostPath:
            path: /var/log/pods

```

### 3.2.通过tar包安装grafana (推荐)
``` 
1)、下载解压
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-10.0.0.linux-amd64.tar.gz
tar -zxvf grafana-enterprise-10.0.0.linux-amd64.tar.gz

2)、启动 
后台启动
nohup ./bin/grafana-server > /opt/logs/grafana.log 2>&1 &

添加启动服务
vim /usr/lib/systemd/system/grafana-server.service

[Unit]
Description=Grafana
After=network.target

[Service]
Type=notify
ExecStart=/opt/grafana/bin/grafana-server -homepath /opt/grafana
Restart=on-failure

[Install]
WantedBy=multi-user.target 
```
gtafana：http://ip:3000/ 默认端口3000，默认账号 admin/admin, 首次登录修改密码

### 3.3 配置loki数据源
1.查看loki-outer对应nodeport类型的暴露的端口loki_nodeport_port   
```kubectl get svc -n logging ``` 

2.添加loki数据源  
```http://k8snode_ip(如果有vip可以直接配置vip):loki_nodeport_port ``` 

## 四、问题记录
### loki参数优化：

### loki接s3存储：

### grafana权限配置
- 超管：账号-admin，角色role-admin
- Team：建议创建dev-Aproduct、dev-Bproduct、ops等不同的team，方便给不同team分配不同项目dashboard权限
- User：创建user，dev赋viewer权限，ops赋editor权限
- 编辑Team：add user，选择user，可以作为team管理员或者member
- dashboard权限：
  - 项目文件夹权限：permissions，可添加team或者user，给view或editor权限
  - 子dashboard权限：继承自文件夹权限，可添加team或者user，给view或editor权限
