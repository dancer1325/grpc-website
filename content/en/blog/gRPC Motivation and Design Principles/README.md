## Motivation
* Stubby
  * Check '../about'
  * reasons to use it
    * utility in microservice architecture 
    * efficiency
    * security
    * reliability
  * inconveniences
    * 👁️NOT based on standards👁️
      * -> NOT applicable to
        * mobile
        * IoT
        * Cloud
    * coupled to Google-infrastructure

## Principles
* Services, NOT objects & Messages, NOT References
  * == microservices philosophy /
    * grained message exchange &
    * avoiding
      * [pitfalls of distributed systems](https://martinfowler.com/articles/distributed-objects-microservices.html)
      * [fallacies of ignoring network](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing)
* Coverage & Simplicity
  * coverage 
    * == available for any
      * popular development platform &
      * devices / CPU & memory-limited
* Free & Open
* Interoperability & Reach
  * interoperability -- transversal to -- internet infrastructure
* General purpose & performant
  * == broad class of use-cases / it's performance < (just a little) performance of specific stack
* Layered
* Payload agnostic
* Streaming
  * use cases
    * in storage systems -- for expressing -- large data-sets
    * in voice-to-text -- for representing -- temporally related messages
* Blocking & Non-Blocking
  * == client -- exchanges sync & async message with -- server
* Cancellation & Timeout
  * cancellation
    * allows
      * servers -- can reclaim resources to -- clients
* Lameducking
  * == servers gracefully shut-down
    * == reject new requests & keep on processing in-flight
* Flow control
  * allows
    * better buffer management
    * protecting from DOS
* Pluggable
  * == allowed extensions within API infrastructure
* Extensions as APIs
  * uses
    * service1 -- collaborates with -- service2
* Metadata exchange
  * uses
    * authentication
    * tracing
  * -- independent to -- APIs/exposed by services
* Standardize status codes
  * == pretty constrained
    * if you need wider -> use metadata exchange


## Notes
* large distributed systems need
  * security
  * health-checking
  * load-balancing
  * failover
  * monitoring
  * tracing
  * logging