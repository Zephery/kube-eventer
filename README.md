## kube-eventer    

<p align="center">
	<img src="docs/logo/kube-eventer.png" width="150px" />   
  <p align="center">
    kube-eventer emit kubernetes events to sinks
  </p>
</p>

### Overview 
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
[![Build Status](https://travis-ci.org/AliyunContainerService/kube-eventer.svg?branch=master)](https://travis-ci.org/AliyunContainerService/kube-eventer)
[![Codecov](https://codecov.io/gh/AliyunContainerService/kube-eventer/branch/master/graph/badge.svg)](https://codecov.io/gh/AliyunContainerService/kube-eventer)    

kube-eventer is an event emitter that sends kubernetes events to sinks(.e.g, dingtalk,sls,kafka and so on). The core design concept of kubernetes is state machine. So there will be `Normal` events when transfer to desired state and `Warning` events occur when to unexpected state. kube-eventer can help to diagnose, analysis and alarm problems.

### Architecture diagram
<p align="center">
	<img src="docs/images/arch.png" width="500px" />   
  <p align="center">
    Architecture diagram of kube-eventer
  </p>
</p>   

### Usage 
Here is a demo of kube-eventer with dingtalk sink. Dingtalk is an All-in-one Mobile Workplace like slack. Dingtalk sink can get kubernetes events from eventer. You can do chatops with it. Try it following the steps below.     

0. Get dingtalk token 
Add a chatbot in a dingtalk group and get bot token.   
<img width=600px src="docs/images/token.png"/>    
 
1. Install eventer and configure sink 
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: kube-eventer
  namespace: kube-system
spec:
  replicas: 1
  template:
    metadata:
      labels:
        task: monitoring
        k8s-app: kube-eventer
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
    spec:
      serviceAccount: admin
      containers:
      - name: kube-eventer
        image: registry.cn-hangzhou.aliyuncs.com/acs/kube-eventer-amd64:v1.0.0-d9898e1-aliyun 
        imagePullPolicy: IfNotPresent
        command:
        - /eventer
        - --source=kubernetes:https://kubernetes.default
        ## .e.g,dingtalk sink demo
        - --sink=dingtalk:[your_webhook_url]&label=[your_cluster_id]&level=[Normal or Warning(default)]
```
2. View events in dingtalk
<img width=600px src="docs/images/dingtalk.jpeg"/>

### Sink Configure 
Supported Sinks:

| Sink Name                    | Description                       |
| ---------------------------- | :-------------------------------- |
| <a href="docs/en/dingtalk-sink.md">dingtalk</a>      | sink to dingtalk bot              |
| <a href="docs/en/sls-sink.md">sls</a>           | sink to alibaba cloud sls service |
| <a href="docs/en/elasticsearch-sink.md">elasticsearch</a> | sink to elasticsearch             |
| <a href="docs/en/honeycomb-sink.md">honeycomb</a>     | sink to honeycomb                 |
| <a href="docs/en/influxdb-sink.md">influxdb</a>      | sink to influxdb                  |
| <a href="docs/en/kafka-sink.md">kafka</a>         | sink to kafka                     |
| <a href="docs/en/log-sink.md">log</a>               | sink to standard output           |

### Contributing 
Please check <a href="docs/en/CONTRIBUTING.md" target="_blank">CONTRIBUTING.md</a>


### License 
This software is released under the Apache 2.0 license.
