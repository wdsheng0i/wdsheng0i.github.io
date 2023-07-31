---
layout: post
title: 云原生技术栈
category: dev-ops
tags: [dev-ops]
---

## 参考资料
- [云原生Java架构师的第一课K8s+Docker+KubeSphere+DevOps【尚硅谷】](https://www.bilibili.com/video/BV13Q4y1C7hS?spm_id_from=333.999.0.0&vd_source=fc861ca405c9ae969dbc2f0dac72ba4b)
- [云原生/微服务架构/运维 系列课程](https://blog.csdn.net/luanpeng825485697/category_11735474.html)

## 一、云原生
### 1.1 什么是云原生
云原生是基于分布部署和统一运管的分布式云 ，以容器、微服务、DevOps等技术为基础建立的一套云技术产品体系。

2015年云原生计算基金会（CNCF）成立，最初把云原生定义为包括：容器化封装 + 自动化管理 + 面向微服务；2018年，CNCF又更新了云原生的定义，把 服务网格(Service Mesh) 和 声明式API 给加了进来

云原生是一种行为方式和设计理念，究其本质，凡是能够提高云上资源利用率和应用交付效率的行为或方式都是云原生的。

云计算的发展史就是一部云原生化的历史。Kubernetes 开启了云原生的序幕，服务网格 Istio 的出现，引领了后 Kubernetes 时代的微服务，serverless 的再次兴起，使得云原生从基础设施层不断向应用架构层挺进

> 云原生（cloud native）：云环境 + 微服务 + Docker容器化 + k8s容器编排 + CICD ，从设计初即考虑云环境、微服务、容器化、持续部署、弹性扩容
    

![](https://wdsheng0i.github.io/assets/images/2021/k8s/cloud-native.png)     

### 1.2 为何使用云原生应用架构
- 效率高、敏捷
- 安全、可靠
- 弹性、易扩展 
- 故障隔离保护 不中断业务持续更新

## 二、云原生技术栈
参考文档：https://landscape.cncf.io/  

|类型	|名称	|版本	|CNCF项目情况	|说明 |
|----	|----	|----	|----	|---- |
|应用定义和开发-应用定义  |Helm  |stable  |  毕业  |  |
|应用定义和开发-应用定义  |Kustomize  |stable  |  /  |轻量化，kubectl内置支持  |
|应用定义和开发-应用定义  |Operator Framework  |stable  |  孵化  |有状态应用构建流行标准  |
|应用定义和开发-CICD  |GitLab  |2013.8.8  |  /  |GitOps中CI实践  |
|应用定义和开发-CICD  |Argo CD  |stable  |  孵化  |  |
|编排和管理-容器编排  |Kubernetes  |1.19  |  毕业  |一年发版3次，API迭代迅速  |
|编排和管理-服务发现  |CoreDNS  |1.6.9  |  毕业  |K8S默认使用  |
|编排和管理-服务发现  |etcd  |3.3.12  |  毕业  |K8S 1.22版本默认支持  |
|编排和管理-服务代理  |kubernetes/ingress-nginx  |0.30.0  |  /  |  |
|运行时-CRI  |containerd  |1.4.3  |  毕业  |已替换docker作为默认容器运行时  |
|运行时-CNI  |Cilium  |stable  |  /  |原生基于eBPF，提供加密网络，内置Hubble工具提供网络可观测性  |
|运行时-CSI  |Rook  |stable  |  毕业  |可连接内部外部ceph/NFS等多种存储作为SC  |
|基础设施-镜像管理  |Harbor  |stable  |  毕业  |  |
|基础设施-K8S部署  |Kubespray  |2.16  |  /  |基于ansible，组件可配置，可提供离线部署  |
|基础设施-K8S部署  |kubeadm  |1.19  |  /  |快速统一进行K8S部署及管理  |
|基础设施-操作系统  |Debian  |buster  |  /  |基于4.19 LTS内核，兼顾稳定性及新特性  |
|基础设施-负载均衡  |HAProxy  |stable  |  /  |提供四层负载均衡服务  |

### 2.1 容器技术选型
#### 2.1.1 Docker
- Docker：是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中,然后发布到任何流行的Linux机器或Windows 机器上, 也可以实现虚拟化,

容器是完全使用沙箱机制, 相互之间不会有任何接口, 更重要的是容器性能开销极低。 

#### 2.1.2 Cloud Foundry
- Cloud Foundry是业界第一个开源PaaS云平台，它支持多种框架、语言、运行时环境、云平台及应用服务，使开发人员能够在几秒钟内进行应用程序的部署和扩展，无需担心任何基础架构的问题。
  
Cloud Foundry基于容器的架构支持各类云服务供应商，同时支持以任何编程语言运行的应用程序

### 2.2 容器编排工具选型

#### 2.2.1 Mesos Marathon
- Mesos：是实现分布式计算的框架，在分布式计算过程中，Mesos会对上述计算机资源（内存，CPU，网络IO等）进行管理和分配调度。
- Marathon：是基于MesosFramework的开源框架, 就是一个基于mesos的编排工具，它通过与mesos-master通信，调用接口，实现通用的编排策略。 而Marathon自身又提供了接口，使得应用程序的部署得以更简单

#### 2.2.2 Docker Compose
- Docker compose：是一个命令行工具,是用于定义和运行多容器Docker应用程序的工具;通过Compose,开发者可以使用YML文件来配置应用程序需要的所有服务

#### 2.2.3 Docker Swarm
- Docker Swarm是一个Dockerized化的分布式应用程序的本地集群，它是在Machine所提供的功能的基础上优化主机资源的利用率和容错服务  

是Docker公司推出的用来管理docker集群的平台

#### 2.2.4 Kubernetes
- Kubernetes：是Google开源的一个容器编排引擎，它支持自动化部署、大规模可伸缩、应用容器化管理。在

### 2.3 容器Docker相关工具

#### 2.3.1 Docker registry私仓

#### 2.3.2 Portainer
Portainer 是一个轻量级的管理UI ，可让你轻松管理不同的 Docker 环境（Docker 主机或 Swarm 群集）  
Portainer是一个可视化的容器镜像的图形管理工具，利用Portainer可以轻松构建，管理和维护Docker环境。 而且完全免费，基于容器化的安装方式，方便高效部署。  

官方站点：https://www.portainer.io/    
安装Portainer-官方安装说明：https://www.portainer.io/installation/

#### 2.3.3 Harbor
- Harbor：是为企业用户设计的容器镜像仓库开源项目，包括了权限管理(RBAC)、LDAP、审计、安全漏洞扫描、镜像验真、管理界面、自我注册、HA 等企业必需的功能，同时针对中国用户的特点，设计镜像复制和中文支持等功能

### 2.4 容器编排k8s相关工具

#### 2.4.1 Rancher
- Rancher：是用于部署和管理Kubernetes完整解决方案以及容器云管理平台。与单个安装程序或单个平台相比,通过Rancher管理成千上万个云托管Kubernetes集群

#### 2.4.2 ArgoCD持续部署
- ArgoCD：是一个为 Kubernetes 而生的，遵循声明式 GitOps 理念的持续部署（CD）工具，它的配置和使用非常简单，并且自带一个简单易用的 Dashboard 页面，并且支持多种配置管理/模板工具（例如 Kustomize、Helm、Ksonnet、Jsonnet、plain-YAML）。

#### 2.4.3 KubeSphere
- KubeSphere：是在Kubernetes 之上构建的面向云原生应用的分布式操作系统,完全开源,支持多云与多集群管理,提供全栈的 IT 自动化运维能力,简化企业的 DevOps 工作流

#### 2.4.4 kind、Kubespray 创建 K8S 集群
- Kind：是Kubernetes In Docker的缩写，顾名思义，看起来是把k8s放到docker的意思。没错，kind创建k8s集群的基本原理就是：提前准备好k8s节点的镜像，通过docker启动容器，来模拟k8s的节点，从而组成完整的k8s集群。需要注意，kind创建的集群仅可用于开发、学习、测试等，不能用于生产环境
- Kubespray：是Kubernetes incubator 中的项目，目标是提供Production Ready Kubernetes部署方案，该项目基础是通过Ansible Playbook 来定义系统与Kubernetes 集群部署的任务

#### 2.4.5 CNI容器网络插件、Cilium、Flannel
- CNI插件存放位置:# cat /etc/cni/net.d/10-flannel.conflist    插件使用的解决方案如下: 虚拟网桥,虚拟网卡,多个容器共用一个虚拟网卡进行通信
- Cilium：是一个用于容器网络领域的开源项目，主要是面向容器而使用，用于提供并透明地保护应用程序工作负载（如应用程序容器或进程）之间的网络连接和负载均衡。
- Rudder 已改名为Flannel，为每个使用 Kubernetes 的机器提供一个子网。也就是说 Kubernetes 集群中的每个主机都有自己一个完整的子网，例如机器 A 和 B 可以有 10.0.1.0/24 和 10.0.2.0/24 子网。该模型的好处就是降低端口映射的复杂度

#### 2.4.6 Helm包管理器、kustoimze配置管理
- Helm：是 kubernetes的包管理器，包管理器类似于我们在ubuntu中使用的apt，在centos中使用的yum 或者python中的pip一样，能够快速查找，下载和安装软件包。helm由客户端组件helm和服务端组件Tiller组成，能够将一组众多分散的k8s资源打包统一管理，是查找、共享和使用为kubernetes构建软件的最佳方式
- kustomize：是 kubernetes 原生的配置管理，以无模板方式来定制应用的配置。  

kustomize 使用 k8s 原生概念帮助创建并复用资源配置(YAML)，允许用户以一个应用描述文件 （YAML 文件）为基础（Base YAML），然后通过 Overlay 的方式生成最终部署应用所需的描述文件

#### 2.4.7 kubelet、kubeadm、kubectl
- kubeadm是官方社区推出的一个用于快速部署kubernetes集群的工具。
    - 这个工具能通过两条指令完成一个kubernetes集群的部署：　# 创建一个 Master 节点　kubeadm init　# 将一个 Node 节点加入到当前集群中　kubeadm join <Master节点的IP和端口 >　
- kubectl是Kubernetes集群的命令行工具，通过kubectl能够对集群本身进行管理，并能够在集群上进行容器化应用的安装和部署
- kubelet　master派到node节点代表，管理本机容器，一个集群中每个节点上运行的代理，它保证容器都运行在Pod中，负责维护容器的生命周期，同时也负责Volume(CSI) 和 网络(CNI)的管理　

## 三、容器应用的设计原则 
12-Factor：https://12factor.net/zh_cn/

### 3.1. 单一职责
每一个容器应该提供单一的职责，由研发架构师把控。

### 3.2. 高可观测性
2.2.1. 探针  
- 存活探针（LivenessProbe）
- 就绪探针（ReadnessProbe）
- 启动探针（StartupProbe）

2.2.2. 指标  
暴露Prometheus的Metrics。

2.2.3. 日志  
日志输出到标准输出(STDOUT)和标准出错(STDERR)。

2.2.4. 链路追踪  

### 3.3. 生命周期确认

### 3.4. 镜像不可变
研发、测试、生产所有环境都要使用同一个镜像。

### 3.5. 用完即丢
所有的容器都是临时的，它随时有可能被其它的容器实例所替代，需要把容器的状态保存在容器之外，并且尽可能快速的启动和终止容器。即容器为无状态的。  
挂载日志、配置文件？？

### 3.6. 自包含
容器在构建的时候应该包含所有的依赖，也就是所说容器在运行时不应该有任何的外部依赖。

### 3.7. 限制运行资源
容器的最佳实践应该是在运行时指定容器对资源配置的需求。例如需要多少的内存，CPU等等。这样做可以使得容器编排能都更有效的调度和管理资源。

### 3.8 Dockerfile编写最佳实践
a. 减少镜像层  
一次RUN指令形成新的一层，尽量Shell命令都写在一行，减少镜像层。

b. 优化镜像大小  
一次RUN形成新的一层，如果没有在同一层删除，无论文件是否最后删除，都会带到下一层，所以要在每一层清理对应的残留数据，减小镜像大小。

c. 选取统一基底镜像  