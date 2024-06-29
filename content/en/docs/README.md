* Check the subsections

# gRPC supported language & platform & OS
* [Check the table](https://grpc.io/docs/#official-support)

# Protocol Buffers
* Check [protocol buffers documentation](https://protobuf.dev)
* used by gRPC for
  * service definitions &
  * data serialization
* `protoc`
  * protocol buffer compiler
    * allows
      * '.proto' files -- are compiled (at build time) -- generating classes / simple
        * accessors &
        * methods to serialize & parse
  * how to install it ?
    * via
      * package manager
        * TODO:
        * [macOS] `brew install protobuf`
      * pre-compiled binaries
        * TODO:
    * `protoc --version`
      * check the installed version
