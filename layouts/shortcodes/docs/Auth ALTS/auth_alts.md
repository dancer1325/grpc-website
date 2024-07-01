### Overview
* Application Layer Transport Security (ALTS)
  * := mutual authentication and transport encryption system /
    * developed by Google
    * connections are end-to-end protected with privacy and integrity.
  * uses
    * securing RPC communications within Google's infrastructure
  * vs mutual TLS
    * designed and optimized to meet the needs of Google's production environments
      * Check [ALTS whitepaper](https://cloud.google.com/security/encryption-in-transit/application-layer-transport-security)
  * features in gRPC
    * create gRPC servers & clients / ALTS as the transport security protocol
    * Applications can access peer information 
      * _Example:_ peer service account.
    * support-
      * client authorization &
      * server authorization
    * if you want to enable ALTS -> minimal code changes 
  * requirements to set ALTS fully functional
    * application runs on
      * [Compute Engine](https://cloud.google.com/compute) or
      * [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine)

{{ with .Page.Params.code.client_credentials -}}

### gRPC Client / uses ALTS Transport Security Protocol
* ALTS credentials
  * uses in gRPC clients
    * connect to servers -- _Example:_ --

{{ . | safeHTML }}
{{ end }}

{{ with .Page.Params.code.server_credentials -}}

### gRPC Server / uses ALTS Transport Security Protocol
* ALTS credentials
  * uses in gRPC servers
    * allow clients to connect to them -- _Example:_ --

{{ . | safeHTML }}
{{ end }}

{{ with .Page.Params.code.server_authorization -}}

### Server Authorization / using ALTS
* built-in gRPC
* gRPC client -- via ALTS, can set -- the expected server service accounts prior to establishing a connection
  * -> At the end of the handshake, server authorization guarantees that the 
    * server identity == one of the service accounts / specified by the client
    * otherwise ->  the connection fails

{{ . | safeHTML }}
{{ end }}

{{ with .Page.Params.code.client_authorization -}}

### Client Authorization
* if the connection is successful -> peer information (e.g., client’s service account) is stored in the [AltsContext][]
* gRPC provides a utility library for client authorization check
* if the server knows the expected client identity (e.g., `foo@iam.gserviceaccount.com`) -> it can run the following example codes, to authorize the incoming RPC

[AltsContext]: https://github.com/grpc/grpc/blob/master/src/proto/grpc/gcp/altscontext.proto

{{ . | safeHTML }}
{{ end }}
