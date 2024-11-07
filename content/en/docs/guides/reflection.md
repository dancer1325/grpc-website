---
title: Reflection
description: >-
  Explains how reflection can be used to improve the transparency and
  interpretability of RPCs.
---

### Overview

* Reflection
  * := [protocol](https://github.com/grpc/grpc-proto/blob/master/grpc/reflection/v1/reflection.proto)
  * uses
    * 👀by gRPC servers -- to declare the -- protobuf-defined APIs + ALL types👀
      * APIs / they export -- over a -- standardized RPC service
      * types / referenced by the
        * request messages
        * response messages 
      * -> used by Clients to
        * encode requests
        * decode responses | human-readable manner
    * debugging tools
      * [`grpcurl`](https://github.com/fullstorydev/grpcurl)
      * [Postman](https://learning.postman.com/docs/sending-requests/grpc/grpc-client-overview/)
  * gRPC reflection API == | REST world, serve an OpenAPI document | HTTP server / has the REST API 

### Transparency and Interpretability

* Protobuf for serialization
  * == protocol
    * _binary_
    * non-human-readable
  * allows
    * speeding up an RPC
  * cons
    * more difficult / error prune / timing to -- manually interact with a -- server
      * if you want to manually send -- a gRPC request | HTTP/2, via `curl`, to a -- server -> 
        1. Know which RPC services / exposed by the server
        2. Know the protobuf definition of the
           1. request message & ALL types / references
           2. response message & ALL types / references
        3. hand-craft
           1. your request message(s) -- binary
           2. -- decode the -- response message(s)
      * reflection protocol
        * 👀alternative 👀

### Enabling Reflection on a gRPC Server

* Reflection
  * 👀NOT automatically enabled | gRPC server 👀 
  * how to enable?
    * 👀call additional functions / -- depend on the -- language 👀
    
    | Language | Guide            |
    |----------|------------------|
    | Java     | [Java example]   |
    | Go       | [Go example]     |
    | C++      | [C++ example]    |
    | Python   | [Python example] |

[Java example]: https://github.com/grpc/grpc-java/tree/master/examples/example-reflection 

[Go example]: https://github.com/grpc/grpc-go/tree/master/examples/features/reflection 

[C++ example]: https://github.com/grpc/grpc/tree/master/examples/cpp/reflection

[Python example]: https://github.com/grpc/grpc/blob/master/examples/python/helloworld/greeter_server_with_reflection.py

### Tips
 
* Reflection works seamlessly 
  * with SOME tools _Example:_ `grpcurl`
  * if reflection is exposed
    * if your gRPC API is accessible to public users -> NOT recommended to expose the
reflection service
