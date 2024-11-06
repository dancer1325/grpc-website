---
title: gRPC Motivation and Design Principles
date: 2015-09-08
author:
  name: Louis Ryan
  position: Google
---

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

## Principles & Requirements

### Services != Objects, Messages != References

* == microservices philosophy /
  * grained message exchange &
  * avoiding
    * [pitfalls of distributed systems](https://martinfowler.com/articles/distributed-objects-microservices.html)
    * [fallacies of ignoring network](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing)

### Coverage & Simplicity

* stack -- should be -- 
  * available | ANY
    * popular development platform &
    * devices / CPU & memory-limited
  * easy to build

### Free & Open

* free
  * fundamental features 
* open-source
  * artifacts
    * == NOT impediments to adopt

### Interoperability &amp; Reach

* wire protocol -- must be -- independent to internet infrastructure

### General Purpose &amp; Performant

* == broad class of use-cases / it's performance < (just a little) performance of specific stack

### Layered

* stack -- must be able to independently -- evolve /
  * NOT break application layers

### Payload Agnostic

* protocol and implementations -- must allow -- different
  * message types
  * pluggable encodings (_Example:_ protocol buffers, JSON, XML)

### Streaming

* use cases
  * storage systems -- for expressing -- large data-sets
  * voice-to-text OR stock-tickers -- for representing -- temporally related messages sequences

### Blocking &amp; Non-Blocking

* 👀== client -- exchanges sync & async message with -- server 👀
* uses
  * scaling | certain platforms
  * handling streams | certain platforms

### Cancellation &amp; Timeout

* cancellation
  * allows
    * if clients are well-behaved -> servers -- can reclaim resources to -- clients
      * 🧠Reason to reclaim: 🧠 operations can be expensive & long-lived 
  * if causal-chain of work is tracked -> cancellation can cascade
* timeout
  * if a client gets a timeout / call -> services can tune their behavior -- based on -- needs of the client

### Lameducking

* == servers gracefully shut-down
  * == reject new requests & keep on processing in-flight

### Flow Control

* Flow control
  * allows
    * better buffer management
    * protecting from DOS
      * _Example of DOS:_ overly active peer
  * use cases
    * balance
      * computing power
      * network capacity between client and server

### Pluggable

* wire protocol
  * 👀== 1 part -- of a -- functioning API infrastructure 👀
* large distributed systems -- need --
  * security
  * health-checking
  * load-balancing
  * failover
  * monitoring
  * tracing
  * logging
* Implementations -- should provide -- 
  * extensions points == customizable
    * Reason: 🧠 allow for plugging | previous features 🧠
  * default implementations

### Extensions -- as -- APIs

* type of extensions
  * protocol extensions
  * API extensions
    * uses
      * service1 -- collaborates with -- service2
    * could include
      * health-checking,
      * service introspection,
      * load monitoring,
      * load-balancing assignment 

### Metadata Exchange

* Metadata exchange
  * uses
    * authentication
    * tracing
  * 👀-- independent to -- APIs/exposed by services 👀

### Standardized Status Codes

* Standardize status codes
  * == pretty constrained
    * if you need wider -> use metadata exchange
    * Reason: 🧠 make error handling decisions clearer 🧠