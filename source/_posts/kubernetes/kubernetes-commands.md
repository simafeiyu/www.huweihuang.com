---
title: "[Kubernetes] Kubernetes常用命令（kubectl）"
catalog: true
date: 2017-08-13 10:50:57
type: "categories"
subtitle:
header-img: 
tags:
- Kubernetes
catagories:
- Kubernetes
---

## 1. kubectl介绍

kubectl controls the Kubernetes cluster manager.

Usage:
 kubectl [flags] 
 kubectl [command]

```shell
[root@node5 ~]# kubectl --help
kubectl controls the Kubernetes cluster manager.
Find more information at https://github.com/kubernetes/kubernetes.
Usage:
  kubectl [flags]
  kubectl [command]
Available Commands:
  get            Display one or many resources
  describe       Show details of a specific resource or group of resources
  create         Create a resource by filename or stdin
  replace        Replace a resource by filename or stdin.
  patch          Update field(s) of a resource by stdin.
  delete         Delete resources by filenames, stdin, resources and names, or by resources and label selector.
  edit           Edit a resource on the server
  apply          Apply a configuration to a resource by filename or stdin
  namespace      SUPERSEDED: Set and view the current Kubernetes namespace
  logs           Print the logs for a container in a pod.
  restart        Restart a container in a pod.
  rolling-update Perform a rolling update of the given ReplicationController.
  scale          Set a new size for a Replication Controller.
  attach         Attach to a running container.
  exec           Execute a command in a container.
  port-forward   Forward one or more local ports to a pod.
  proxy          Run a proxy to the Kubernetes API server
  run            Run a particular image on the cluster.
  stop           Deprecated: Gracefully shut down a resource by name or filename.
  expose         Take a replication controller, service or pod and expose it as a new Kubernetes Service
  autoscale      Auto-scale a replication controller
  label          Update the labels on a resource
  annotate       Update the annotations on a resource
  config         config modifies kubeconfig files
  cluster-info   Display cluster info
  api-versions   Print the supported API versions on the server, in the form of "group/version".
  version        Print the client and server version information.
  help           Help about any command
Flags:
      --alsologtostderr[=false]: log to standard error as well as files
      --api-version="": The API version to use when talking to the server
      --certificate-authority="": Path to a cert. file for the certificate authority.
      --client-certificate="": Path to a client key file for TLS.
      --client-key="": Path to a client key file for TLS.
      --cluster="": The name of the kubeconfig cluster to use
      --context="": The name of the kubeconfig context to use
      --insecure-skip-tls-verify[=false]: If true, the server's certificate will not be checked for validity. This will make your HTTPS connectionsinsecure.
      --kubeconfig="": Path to the kubeconfig file to use for CLI requests.
      --log-backtrace-at=:0: when logging hits line file:N, emit a stack trace
      --log-dir="": If non-empty, write log files in this directory
      --log-flush-frequency=5s: Maximum number of seconds between log flushes
      --logtostderr[=true]: log to standard error instead of files
      --match-server-version[=false]: Require server version to match client version
      --namespace="": If present, the namespace scope for this CLI request.
      --password="": Password for basic authentication to the API server.
  -s, --server="": The address and port of the Kubernetes API server
      --stderrthreshold=2: logs at or above this threshold go to stderr
      --token="": Bearer token for authentication to the API server.
      --user="": The name of the kubeconfig user to use
      --username="": Username for basic authentication to the API server.
      --v=0: log level for V logs
      --vmodule=: comma-separated list of pattern=N settings for file-filtered logging
 
Use "kubectl [command] --help" for more information about a command.
```

## 2. 操作的资源对象

1. Node
2. Podes
3. Replication Controllers
4. Services
5. Namespace
6. ComponentStatus

## 3. kubectl命令分类[command]

### 3.1 增

1）create:[Create a resource by filename or stdin]

2）run:[ Run a particular image on the cluster]

3）apply:[Apply a configuration to a resource by filename or stdin]

4）proxy:[Run a proxy to the Kubernetes API server ]

### 3.2 删

1）delete:[Delete resources ]

### 3.3 改

1）scale:[Set a new size for a Replication Controller]

2）exec:[Execute a command in a container]

3）attach:[Attach to a running container]

4）patch:[Update field(s) of a resource by stdin]

5）edit:[Edit a resource on the server]

6） label:[Update the labels on a resource]

7）annotate:[Auto-scale a replication controller]

8）replace:[Replace a resource by filename or stdin]

9）config:[config modifies kubeconfig files]

### 3.4 查

1）get:[Display one or many resources]

2）describe:[Show details of a specific resource or group of resources]

3）log:[Print the logs for a container in a pod]

4）cluster-info:[Display cluster info]

5） version:[Print the client and server version information]

6）api-versions:[Print the supported API versions]

## 4. 配置K8S环境

### 4.1 非安全方式

```shell
kubectl config set-cluster k8s --server=http://<url> 
kubectl config set-context <namespace> --cluster=k8s --namespace=<namespace> 

kubectl config use-context <namespace> 
```

### 4.2 安全方式

```shell
kubectl config set-cluster k8s --server=https://<url> --insecure-skip-tls-verify=true
kubectl config set-credentials k8s-user --username=<username> --password=<password>

kubectl config set-context <namespace> --cluster=k8s --user=k8s-user --namespace=<namespace> 
kubectl config use-context <namespace>
```

### 4.3 查询当前配置环境

```shell
[root@mysql2 ]# kubectl cluster-info
Kubernetes master is running at http://192.168.19.2:8081
```

## 5. Pod相关命令

### 5.1 查询Pod

```shell
kubectl get pod -o wide --namespace=<NAMESPACE>
```

### 5.2 进入Pod

```shell
kubectl exec -it <Pod_NAME> /bin/bash --namespace=<namespace>
```

### 5.3 删除Pod

```shell
kubectl delete pod wsop-proxy-1-0-9-ii97y 
```

### 5.4 日志查看

```shell
$ 查看运行容器日志 
kubectl logs wsop-proxy-1-0-9-ii97y --namespace=<namespace>
$ 查看上一个挂掉的容器日志 
kubectl logs wsop-proxy-1-0-9-ii97y -p --namespace=<namespace> 
```

## 6. Node隔离与恢复

说明：Node设置隔离之后，原先运行在该Node上的Pod不受影响，后续的Pod不会调度到被隔离的Node上。

**1. Node隔离**

```shell
kubectl patch node <NodeName> -p '{"spec":{"unschedulable":true}}'
```
**2. Node恢复**

```shell
kubectl patch node <NodeName> -p '{"spec":{"unschedulable":false}}'
```

## 7. kubectl label

**1. 固定Pod到指定机器**

```shell
kubectl label node <NodeName> namespace/<NAMESPACE>=true
```

**2. 取消Pod固定机器**

```shell
kubectl label node <NodeName> namespace/<NAMESPACE>-
```

## 8. kubectl 自动补全

```shell
# for linux
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```
