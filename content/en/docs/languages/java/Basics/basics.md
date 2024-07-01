---
title: Basics tutorial
description: A basic tutorial introduction to gRPC in Java.
weight: 50
---

* Goal
  * Define a service in a `.proto` file -- by [proto3](https://github.com/google/protobuf/releases) -- 
  * via `protoc` -- generate -- server and client code
  * via Java gRPC API -- write -- simple client and server / your service

### Why use gRPC?

{{< why-grpc >}}

### Example code and setup
* Check [grpc/grpc-java/examples/src/main/java/io/grpc/examples/routeguide](https://github.com/grpc/grpc-java/tree/master/examples/src/main/java/io/grpc/examples/routeguide)

### Defining the gRPC service
* & methods `request` & `response` are first step!! 👁️
  * via [protocol buffers](https://protobuf.dev/overview)
    * Check the `.proto` file in [grpc-java/examples/src/main/proto/route_guide.proto](https://github.com/grpc/grpc-java/blob/master/examples/src/main/proto/route_guide.proto)
      * `java_package` file option is specified, because we're generating Java code
      
        ```proto
            option java_package = "io.grpc.examples.routeguide";
        ```
    
        * == package to use for our generated Java classes 
        * If you do NOT specify it -> default proto package -- using the "package" keyword
        * 👁️If we generate code in another language from this `.proto` -> `java_package` option has NO effect 👁️
        * Generally, NOT made good Java packages
          * Reason: 🧠proto packages are NOT expected to start with reverse domain names❓ 
* How to define a service in '.proto'?
  
  ```
  service serviceName {
    rpc methodName(requestTypes) returns (responseTypes) {}              // simple RPC
    rpc methodName(requestTypes) returns (steam responseTypes) {}        // server-side streaming RPC
    rpc methodName(stream requestType) returns (responseType) {}         // client-side streaming RPC
    rpc methodName(stream requestType) returns (stream responseType) {}  // bidirectional streaming RPC
  }
  ```
  * _Example:_  
    ```proto
    service RouteGuide {
      rpc GetFeature(Point) returns (Feature) {} 
      ...
    }
    ```
  * allowed service's methods
    * *simple RPC* / client 
      * -- sends a request, and waits for a response, via the stub to the -- server ( == normal function call)
      * `rpc methodName(requestType) returns (responseType) {}`

    ```proto
    // Obtains the feature at a given position.
    rpc GetFeature(Point) returns (Feature) {}
    ```
    * *server-side streaming RPC* / client
      * -- sends a request -- to the server
      * gets a stream to read a sequence of messages back 
      * reads from the returned stream / there are NO more messages
      * `rpc methodName(requestType) returns (stream responseType) {}`

    ```proto
    // Obtains the Features available within the given Rectangle.  Results are
    // streamed rather than returned at once (e.g. in a response message with a
    // repeated field), as the rectangle may cover a large area and contain a
    // huge number of features.
    rpc ListFeatures(Rectangle) returns (stream Feature) {}
    ```

    * *client-side streaming RPC* / client
      * writes a sequence of messages
      * sends them -- via provided stream to --  the server
      * waits for the server to
        * read them all &
        * return its response
      * `rpc methodName(stream requestType) returns (responseType) {}`

        ```proto
        // Accepts a stream of Points on a route being traversed, returning a
        // RouteSummary when traversal is completed.
        rpc RecordRoute(stream Point) returns (RouteSummary) {}
        ```

    * *bidirectional streaming RPC* / 
      * both sides send a sequence of messages -- via -- read-write stream
        * two streams operate independently
          * -> clients and servers can read and write in whatever order they like
            * _Example1:_ server could wait to receive all the client messages before writing its responses, or
            * _Example2:_ server could alternately read a message, then write a message, or
          * order of messages / stream is preserved
      * `rpc methodName(stream requestType) returns (stream responseType) {}`

        ```proto
        // Accepts a stream of RouteNotes sent while a route is being traversed,
        // while receiving other RouteNotes (e.g. from other users).
        rpc RouteChat(stream RouteNote) returns (stream RouteNote) {}
        ```
* Protocol buffer message / 👁️ALL request and response types 👁️ -- used in -- our service methods
  * _Example:_

    ```proto
    // Points are represented as latitude-longitude pairs in the E7 representation
    // (degrees multiplied by 10**7 and rounded to the nearest integer).
    // Latitudes should be in the range +/- 90 degrees and longitude should be in
    // the range +/- 180 degrees (inclusive).
    message Point {
      int32 latitude = 1;
      int32 longitude = 2;
    }
    ```

### Generating client and server code
* From our `.proto` service definition -- generate via `protoc` for [proto3](https://github.com/google/protobuf/releases) -- gRPC client and server interfaces
  * Check [grpc-java README][] to build
  * `Feature.java`, `Point.java`, `Rectangle.java`, and others
    * generated in "grpc-java/examples/target/classes/io/grpc/examples/routeguide"
    * protocol buffer code to populate, serialize, and retrieve -- in our -- request and response message types
  * `RouteGuideGrpc.java`
    * == `RouteGuideGrpc.RouteGuideImplBase` + *stub* classes
      * `RouteGuideGrpc.RouteGuideImplBase`
        * == base class for `RouteGuide` servers to implement / ALL methods defined in the `RouteGuide` service 
      * *stub* classes / 
        * clients -- can use to talk to -- a `RouteGuide` server

### Creating the server {#server}
* Check `RouteGuideServer` in [grpc-java/examples/src/main/java/io/grpc/examples/routeguide/RouteGuideServer.java](https://github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/routeguide/RouteGuideServer.java)
* Main parts
  * Override the service base class generated from our service definition -- `extends RouteGuideGrpc.RouteGuideImplBase` --
  * Running a gRPC server to -- `RouteGuideServer.start()` --
    * listen for requests from clients
    * and return the service responses 

#### Implementing RouteGuide
* `RouteGuideServer.RouteGuideService` class 

    ```java
    private static class RouteGuideService extends RouteGuideGrpc.RouteGuideImplBase {
    ...
    }
    ```

  * 👁️implements ALL our service methods 👁️

##### Simple RPC
* `GetFeature(Point request, StreamObserver<Feature> response)`
  * from the client -- gets -- a `Point` (== request)
    * `checkFeature()`
  * from its database -- returns -- the corresponding feature information in a `Feature` (== response observer)
    * `onNext()`
  * mark the RPC as finished 
    * `onCompleted()`

      ```java
      @Override
      public void getFeature(Point request, StreamObserver<Feature> responseObserver) {
        responseObserver.onNext(checkFeature(request));
        responseObserver.onCompleted();
      }
    
      ...
    
      private Feature checkFeature(Point location) {
        for (Feature feature : features) {
          if (feature.getLocation().getLatitude() == location.getLatitude()
              && feature.getLocation().getLongitude() == location.getLongitude()) {
            return feature;
          }
        }
    
        // No feature was found, return an unnamed feature.
        return Feature.newBuilder().setName("").setLocation(location).build();
      }
      ```

##### Server-side streaming RPC
* `listFeatures(Rectangle request, StreamObserver<Feature> responseObserver)`
  * multiple `Feature`s -- are sent back to -- our client
  * mark the RPC as finished
    * `onCompleted()`

      ```java
      private final Collection<Feature> features;
    
      ...
    
      @Override
      public void listFeatures(Rectangle request, StreamObserver<Feature> responseObserver) {
        int left = min(request.getLo().getLongitude(), request.getHi().getLongitude());
        int right = max(request.getLo().getLongitude(), request.getHi().getLongitude());
        int top = max(request.getLo().getLatitude(), request.getHi().getLatitude());
        int bottom = min(request.getLo().getLatitude(), request.getHi().getLatitude());
    
        for (Feature feature : features) {
          if (!RouteGuideUtil.exists(feature)) {
            continue;
          }
    
          int lat = feature.getLocation().getLatitude();
          int lon = feature.getLocation().getLongitude();
          if (lon >= left && lon <= right && lat >= bottom && lat <= top) {
            responseObserver.onNext(feature);
          }
        }
        responseObserver.onCompleted();
      }
      ```

##### Client-side streaming RPC
* `StreamObserver<Point> recordRoute(final StreamObserver<RouteSummary> responseObserver)`
  * anonymous `StreamObserver` is instantiated /
    * `onNext()`
      * get features & other information / each time, client -- writes a `Point` to -- message stream
    * `onCompleted()`

          ```java
          @Override
          public StreamObserver<Point> recordRoute(final StreamObserver<RouteSummary> responseObserver) {
            return new StreamObserver<Point>() {
              int pointCount;
              int featureCount;
              int distance;
              Point previous;
              long startTime = System.nanoTime();
    
              @Override
              public void onNext(Point point) {
                pointCount++;
                if (RouteGuideUtil.exists(checkFeature(point))) {
                  featureCount++;
                }
                // For each point after the first, add the incremental distance from the previous point
                // to the total distance value.
                if (previous != null) {
                  distance += calcDistance(previous, point);
                }
                previous = point;
              }
    
              @Override
              public void onError(Throwable t) {
                logger.log(Level.WARNING, "Encountered error in recordRoute", t);
              }
    
              @Override
              public void onCompleted() {
                long seconds = NANOSECONDS.toSeconds(System.nanoTime() - startTime);
                responseObserver.onNext(RouteSummary.newBuilder().setPointCount(pointCount)
                    .setFeatureCount(featureCount).setDistance(distance)
                    .setElapsedTime((int) seconds).build());
                responseObserver.onCompleted();
              }
            };
          }
          ```

##### Bidirectional streaming RPC
* `StreamObserver<RouteNote> routeChat(final StreamObserver<RouteNote> responseObserver)`

    ```java
    @Override
    public StreamObserver<RouteNote> routeChat(final StreamObserver<RouteNote> responseObserver) {
      return new StreamObserver<RouteNote>() {
        @Override
        public void onNext(RouteNote note) {
          List<RouteNote> notes = getOrCreateNotes(note.getLocation());
    
          // Respond with all previous notes at this location.
          for (RouteNote prevNote : notes.toArray(new RouteNote[0])) {
            responseObserver.onNext(prevNote);
          }
    
          // Now add the new note to the list
          notes.add(note);
        }
    
        @Override
        public void onError(Throwable t) {
          logger.log(Level.WARNING, "Encountered error in routeChat", t);
        }
    
        @Override
        public void onCompleted() {
          responseObserver.onCompleted();
        }
      };
    }
    ```

#### Starting the server for our `RouteGuide` service 
* `ServerBuilder.forPort`
* `serverBuilder.addService`
  * pass an instance of our service implementation class `RouteGuideService`

      ```java
      public RouteGuideServer(int port, URL featureFile) throws IOException {
        this(ServerBuilder.forPort(port), port, RouteGuideUtil.parseFeatures(featureFile));
      }
    
      /** Create a RouteGuide server using serverBuilder as a base and features as data. */
      public RouteGuideServer(ServerBuilder<?> serverBuilder, int port, Collection<Feature> features) {
        this.port = port;
        server = serverBuilder.addService(new RouteGuideService(features))
            .build();
      }
      ...
      public void start() throws IOException {
        server.start();
        logger.info("Server started, listening on " + port);
       ...
      }
      ```

### Creating the client {#client} for our `RouteGuide` service
* Check [grpc-java/examples/src/main/java/io/grpc/examples/routeguide/RouteGuideClient.java](https://github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/routeguide/RouteGuideClient.java)

#### Instantiating a stub
* 👁️requirement previous to create the client 👁️
* types
  * *blocking/synchronous* stub
    * -> RPC call
      * waits for the server to respond &
      * then 
        * return a response or
        * raise an exception
  * *non-blocking/asynchronous* stub
    * -> RPC call
      * NOT wait for the server
      * response is returned asynchronously
    * uses 
      * certain types of streaming call
* steps
  * create a gRPC *channel* for our stub / specifying the server address and port we want to connect to

    ```java
    public RouteGuideClient(String host, int port) {
      this(ManagedChannelBuilder.forAddress(host, port).usePlaintext());
    }
    
    /** Construct client for accessing RouteGuide server using the existing channel. */
    public RouteGuideClient(ManagedChannelBuilder<?> channelBuilder) {
      channel = channelBuilder.build();
      blockingStub = RouteGuideGrpc.newBlockingStub(channel);
      asyncStub = RouteGuideGrpc.newStub(channel);
    }
    ```
  * create our stubs -- via -- methods provided in the generated class `RouteGuideGrpc`

    ```java
    blockingStub = RouteGuideGrpc.newBlockingStub(channel);
    asyncStub = RouteGuideGrpc.newStub(channel);
    ```

#### Calling service methods

##### Simple RPC
* == calling a local method
  * If an error occurs -> it is encoded as a `Status` / we can obtain from the `StatusRuntimeException`

    ```java
    Point request = Point.newBuilder().setLatitude(lat).setLongitude(lon).build();
    Feature feature;
    try {
      feature = blockingStub.getFeature(request);
    } catch (StatusRuntimeException e) {
      logger.log(Level.WARNING, "RPC failed: {0}", e.getStatus());
      return;
    }
    ```

##### Server-side streaming RPC
* == calling a local method
  * `Iterator` is returned / read all the returned `Feature`s

      ```java
      Rectangle request =
          Rectangle.newBuilder()
              .setLo(Point.newBuilder().setLatitude(lowLat).setLongitude(lowLon).build())
              .setHi(Point.newBuilder().setLatitude(hiLat).setLongitude(hiLon).build()).build();
      Iterator<Feature> features;
      try {
        features = blockingStub.listFeatures(request);
      } catch (StatusRuntimeException e) {
        logger.log(Level.WARNING, "RPC failed: {0}", e.getStatus());
        return;
      }
      ```

##### Client-side streaming RPC
* asynchronous stub is required to use
* `StreamObserver` is necessary to be created, which
  * overrides `onNext()` / when the server writes a `RouteSummary` to the message stream  -> prints out the returned information
  * overrides `onCompleted()` / when the *server* has completed the call, reduce a `CountDownLatch` to check if the server has finished writing
  * it's passed to the asynchronous stub

      ```java
      public void recordRoute(List<Feature> features, int numPoints) throws InterruptedException {
        info("*** RecordRoute");
        final CountDownLatch finishLatch = new CountDownLatch(1);
        StreamObserver<RouteSummary> responseObserver = new StreamObserver<RouteSummary>() {
          @Override
          public void onNext(RouteSummary summary) {
            info("Finished trip with {0} points. Passed {1} features. "
                + "Travelled {2} meters. It took {3} seconds.", summary.getPointCount(),
                summary.getFeatureCount(), summary.getDistance(), summary.getElapsedTime());
          }
    
          @Override
          public void onError(Throwable t) {
            Status status = Status.fromThrowable(t);
            logger.log(Level.WARNING, "RecordRoute Failed: {0}", status);
            finishLatch.countDown();
          }
    
          @Override
          public void onCompleted() {
            info("Finished RecordRoute");
            finishLatch.countDown();
          }
        };
    
        StreamObserver<Point> requestObserver = asyncStub.recordRoute(responseObserver);
        try {
          // Send numPoints points randomly selected from the features list.
          Random rand = new Random();
          for (int i = 0; i < numPoints; ++i) {
            int index = rand.nextInt(features.size());
            Point point = features.get(index).getLocation();
            info("Visiting point {0}, {1}", RouteGuideUtil.getLatitude(point),
                RouteGuideUtil.getLongitude(point));
            requestObserver.onNext(point);
            // Sleep for a bit before sending the next one.
            Thread.sleep(rand.nextInt(1000) + 500);
            if (finishLatch.getCount() == 0) {
              // RPC completed or errored before we finished sending.
              // Sending further requests won't error, but they will just be thrown away.
              return;
            }
          }
        } catch (RuntimeException e) {
          // Cancel RPC
          requestObserver.onError(e);
          throw e;
        }
        // Mark the end of requests
        requestObserver.onCompleted();
    
        // Receiving happens asynchronously
        finishLatch.await(1, TimeUnit.MINUTES);
      }
      ```

##### Bidirectional streaming RPC
* syntax for reading and writing here == syntax for our client-streaming method

```java
public void routeChat() throws Exception {
  info("*** RoutChat");
  final CountDownLatch finishLatch = new CountDownLatch(1);
  StreamObserver<RouteNote> requestObserver =
      asyncStub.routeChat(new StreamObserver<RouteNote>() {
        @Override
        public void onNext(RouteNote note) {
          info("Got message \"{0}\" at {1}, {2}", note.getMessage(), note.getLocation()
              .getLatitude(), note.getLocation().getLongitude());
        }

        @Override
        public void onError(Throwable t) {
          Status status = Status.fromThrowable(t);
          logger.log(Level.WARNING, "RouteChat Failed: {0}", status);
          finishLatch.countDown();
        }

        @Override
        public void onCompleted() {
          info("Finished RouteChat");
          finishLatch.countDown();
        }
      });

  try {
    RouteNote[] requests =
        {newNote("First message", 0, 0), newNote("Second message", 0, 1),
            newNote("Third message", 1, 0), newNote("Fourth message", 1, 1)};

    for (RouteNote request : requests) {
      info("Sending message \"{0}\" at {1}, {2}", request.getMessage(), request.getLocation()
          .getLatitude(), request.getLocation().getLongitude());
      requestObserver.onNext(request);
    }
  } catch (RuntimeException e) {
    // Cancel RPC
    requestObserver.onError(e);
    throw e;
  }
  // Mark the end of requests
  requestObserver.onCompleted();

  // Receiving happens asynchronously
  finishLatch.await(1, TimeUnit.MINUTES);
}
```

### Try it out!

Follow the instructions in the [example directory README][] to build and run the
client and server.

[example directory README]: https://github.com/grpc/grpc-java/blob/master/examples/README.md
[grpc-java README]: https://github.com/grpc/grpc-java/blob/master/README.md
