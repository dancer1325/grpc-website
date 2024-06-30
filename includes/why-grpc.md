* Example
  * simple route mapping application /
    * clients can
      * get -- information about features | route
      * create -- summary of their route &
      * exchange -- route information ( *Example:* traffic updates) -- with 
        * server &
        * other clients

* With gRPC, we can
  * define our service in a `.proto` file
  * generate clients and servers in any of gRPC's supported languages / 
    * can be run in environments -- [servers inside a large data center, your own tablet] —
    * 👁️ gRPC handles for you, ALL the complexity of communication between different languages and environments 👁️
  * advantages of working with protocol  buffers
    * efficient serialization,
    * simple IDL,
    * easy interface updating