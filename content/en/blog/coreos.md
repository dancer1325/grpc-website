---
title: gRPC with REST and Open APIs
date: 2016-05-09
author:
  name: Brandon Phillips
  position: "[CoreOS](https://coreos.com)"
  blurb: >
    From this [original post](https://coreos.com/blog/grpc-protobufs-swagger.html), revised by Brandon Phillips, with additional content by Lisa Carey and others at Google.
thumbnail: https://avatars2.githubusercontent.com/u/3730757?v=3&s=200
spelling: cSpell:ignore etcd swaggerscreen
---

* [CoreOS](https://coreos.com/)
  * builds for Linux Containers, open source 
    * projects
    * products
  * products / use gRPC
    * [etcd](https://coreos.com/etcd/)
    * [rkt](https://coreos.com/rkt/)
* Reasons to use gRPC: 
  * 🧠gRPC uses HTTP/2 🧠
    * -> enabling applications to use, | 1! TCP port (available for Go) 
      * HTTP 1.1 REST/JSON API &
      * efficient gRPC interface
  * 🧠 gRPC's easy interoperability -- with -- JSON & [Open API Specification](https://github.com/OAI/OpenAPI-Specification) 🧠
    * ⭐️gRPC services -- ,via open source libraries & API multiplexers, are available in -- flavors ⭐️ 
      * gRPC
      * HTTP REST
    * CoreOS clients -- speak -- HTTP 1.1 + JSON

## A gRPC application called EchoService

* goal
  * small proof-of-concept gRPC application /
    * -- from a -- gRPC API definition
    * add a REST service gateway
    * serve it all | 1! TLS port
    * == web of the shell command echo
      * == service returns, or "echoes", whatever text is sent to it

* gRPC message

    ```proto, name="service.proto"
    # Arguments to EchoService
    message EchoMessage {
     string value = 1;
    }
    ```

* gRPC services
  * gRPC
    * 👀NOT useful as a service / exposes a REST interface 👀

        ```proto
        service EchoService {
          rpc Echo(EchoMessage) returns (EchoMessage) {
          }
        }
        ```
    * you generate -- via `protoc` -> gRPC server   

  * gRPC REST Gateway
    * 👀library / build a RESTful proxy | gRPC EchoService 👀

        ```proto
        service EchoService {
          rpc Echo(EchoMessage) returns (EchoMessage) {
            // ommitted by brevity, to map RPC parameters -- to -- URL paths & query parameters
            option (google.api.http) = {
              post: "/v1/echo"  // // metadata / -- map to a -- RESTful POST method 
              body: "*"      // ALL RPC parameters -- mapped to a -- JSON body
            };
          }
        }
        ```

    * you generate -- via `protoc` -> REST proxy
    * HTTP requests are accepted
      * _Example:_

        ```sh
        $ curl -X POST -k https://localhost:10000/v1/echo -d '{"value": "CoreOS is hiring!"}'
        ```

    <img src="../../../static/img/grpc-rest-gateway.png" class="img-responsive" alt="gRPC API with REST gateway">

* way to distribute the traffic
  * via a Go `http.Handler` / detect if the
    * protocol is HTTP/2 &
    * Content-Type is "application/grpc"

    ```go
    if r.ProtoMajor == 2 && strings.Contains(r.Header.Get("Content-Type"), "application/grpc") {
        grpcServer.ServeHTTP(w, r)
    } else {
        otherHandler.ServeHTTP(w, r)
    }
    ```

* check 
  * [grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway)
  * Example: [link](https://github.com/philips/grpc-gateway-example)
    * Open API UI running | `https://localhost:10000/swagger-ui/#!/EchoService/Echo` 

        <img src="../../../static/img/grpc-swaggerscreen.png" class="img-responsive" alt="gRPC/REST Open API document">
