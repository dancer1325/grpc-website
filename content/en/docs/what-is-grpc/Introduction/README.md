# gRPC
- 👁️ main idea👁️
  - defining a service / specify the methods with arguments & returnTypes
    - **Note:** 👁️ defined in `.proto` files 👁️
- clientApplicationInMachine1 — can call ⚠️ as a local object ⚠️ — serverApplicationInMachine2’s method
  - → easier to create distributed
    - applications &
    - services
  - server side
    - implement the interface (of the previous service definition)
    - runs a gRPC server
  - client side
    - stub / methods == methodsInTheServer
  - ⚠️ can run in any environment & platforms ⚠️
- by default uses Protocol Buffers
  - using
    - as Interface Definition Language (IDL) -- to define the -- service
    - message interchange format
  - → `protoc` + special gRPC plugin
    - → is generated
      - gRPC client code
      - gRPC server code
      - regular Protocol Buffers

# Protocol Buffers
- := mechanism / serialize structured data
  - **Note:** 👁️ also valid with other data formats — *Example:* json —👁️
    - open source
    - created by Google
- how to work with it?
  - define a `.proto` file
    - := text file / contains the structure of datas to serialize
      - `message dataStructuredName {
        fieldsWithName=value
        …
        }`
  - `protoc --proto_path=pathToYourProtoFiles \
    --grpc-gateway_out=logtostderr=true:pathToOutputDirectory \
    yourProtoFile.proto`
    - `protoc`
      - := Compiler for Protocol Buffers
    - — generate — data classes in your preferred language
- versions
  - proto2
    - current default version
  - proto3
    - recommended --  **Reason:** 🧠 use all gRPC-supported languages 🧠 -- 

TODO: Add references o examples for each statement