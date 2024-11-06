---
title: gRPC in Helm
date: 2017-05-15
author:
  name: Brian Hardock
  position: Software engineer at [DEIS](https://deis.com), working on the [Helm](https://helm.sh) project
  guest: true
thumbnail: https://gabrtv.github.io/deis-dockercon-2014/img/DeisLogo.png
---
 
* uses of gRPC & protocol buffers
  * [Helm v2] protocol communication between Helm <-- & --> Tiller
  * model `Chart`
* protobuf files & generated gRPC code vs Swagger
  * aesthetic
  * self-documented
* used from day 1
* benefits of using gRPC & Proto
  * self-documented
    * -> easier to set up client/server communications
  * 👀make easier API involvement 👀
    * Reason: 🧠 reduction in time / spent | boilerplate server code or API modeling 🧠
  * compression capabilities
    * == high performance transmission of data
    * -> perfect for complex Kubernetes applications
* see
  * [Helm proto](https://github.com/kubernetes/helm/tree/master/_proto/hapi)
  * [generated counterparts](https://github.com/kubernetes/helm/tree/master/pkg/proto/hapi)
  * [interface to our Helm client](https://github.com/kubernetes/helm/tree/master/pkg/helm) 
