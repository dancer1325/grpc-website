# Service definition
- Check '../gRPC section'
- possible methods to be defined
  - unary RPCs
    - == 1! request →  1! response  -- _Example:_ `grpc-java` for 'helloworld.proto'
  - server streaming RPCs           -- _Example:_ `grpc-java` for 'route_guide.proto'
    - == 1! request →  stream as response
      - client reads all stream’s messages
      - guaranteed order in stream’s messages  / individual RPC call
  - client streaming RPCs           -- _Example:_ `grpc-java` for 'route_guide.proto'
    - == several messaged → 1! response
      - same as server streaming
  - bidirectional streaming RPCs    -- _Example:_ `grpc-java` for 'route_guide.proto'
    - == client sends messages & server sends messages
      - — via — read-write stream
      - client’s stream — operate independently to — server’s stream
      - guaranteed order in stream’s messages  / stream

# Using the API
* Check '../introduction'
* gRPC infrastructure
  - decodes incoming request
  - execute service methods
  - encodes service responses
- gRPC server
  - implements declared service’s methods     -- _Example:_ `grpc-java` for '../helloworld/HelloWorldServer.java'
- gRPC client
  - `stub` OR client
    - := local object / implements declared service’s methods     -- _Example:_ `grpc-java` for '../helloworld/HelloWorldClient.java'
      - 👁️— to call — gRPC server👁️
TODO: