## 前言
本文基本上是个搬运文章。搬运书本<Designing Distributed Systems>  by Brendan Burns

## 本书官方下载地址
需要注册个Microsoft Azure账户
https://azure.microsoft.com/en-us/resources/designing-distributed-systems/en-us/

## 致谢
本文受到团队成员David 和铁三的指正。David是真的distributed system expert. 铁三是真得填坑专家。

## 本文的意义
1. 设计模式的意义
站在巨人的肩膀上
大家都说的同样的人话
建的轮子能复用

## 单节点模式 (Single-Node Pattern)
### Sidecar模式
比如hpm
比如ngrok
比如dynamic configuration with Sidecars
比如Sync with Git

As Yegge's recalls that one day Jeff Bezos issued a mandate, sometime back around 2002 (give or take a year):

All teams will henceforth expose their data and functionality through service interfaces.
Teams must communicate with each other through these interfaces.
There will be no other form of inter-process communication allowed: no direct linking, no direct reads of another team’s data store, no shared-memory model, no back-doors whatsoever. The only communication allowed is via service interface calls over the network.
It doesn’t matter what technology they use.
All service interfaces, without exception, must be designed from the ground up to be externalizable. That is to say, the team must plan and design to be able to expose the interface to developers in the outside world. No exceptions.

## Ambassador 模式
### Using an Ambassador to Shard a Service
在金服内的用例： api 分流， mobilegw （无线网关）？？？

The following section describes how to use the single-node ambassador pattern for client-side sharding.

Hands On: Implementing a Sharded Redis

### Using an Ambassador for Service Brokering
To understand what this means, imagine a front‐ end that relies on a MySQL database to store its data. In the public cloud, this MySQL service might be provided as software-as-a-service (SaaS), whereas in a private cloud it might be necessary to dynamically spin up a new virtual machine or container run‐ ning MySQL.
Consequently, building a portable application requires that the application know how to introspect its environment and find the appropriate MySQL service to connect to. This process is called service discovery, and the system that performs this discovery and linking is commonly called a service broker.

### Using an Ambassador to Do Experimentation or Request Splitting
比如isupergw (超网)

## Adapter 模式
In the adapter pattern, the adapter container is used to modify the interface of the application container so that it conforms to some predefined interface that is expected of all applications. For exam‐ ple, an adapter might ensure that an application implements a consistent monitoring interface. Or it might ensure that log files are always written to stdout or any number of other conventions.
### Monitoring 
Applying the adapter pattern to monitoring, we see that the application container is simply the application that we want to monitor. The adapter container contains the tools for transforming the monitoring interface exposed by the application container into the interface expected by the general- purpose monitoring system.

使用场景：抓log
Hands On: Using Prometheus for Monitoring
### Logging
Systems might divide their logs into different levels (such as debug, info, warning, and error) with each level going into a different file. Some might simply log to stdout and stderr. This is especially problematic in the world of containerized applications where there is a general expectation that your containers will log to stdout, because that is what is available via commands like docker logs or kubectl logs.

the adapter pattern can help provide a modular, re- usable design for both of these situations. While the application container may log to a file, the adapter container can redirect that file to stdout. Different application con‐ tainers can log information in different formats, but the adapter container can trans‐ form that data into a single structured representation that can be consumed by your log aggregator. Again, the adapter is taking a heterogeneous world of applications and creating a homogenous world of common interfaces.

Hands On: Normalizing Di erent Logging Formats with Fluentd

### Adding a Health Monitor
Consider the task of monitoring the health of an off-the-shelf database container.
比如铁三写的沙箱health monitor script的想法
The database runs in the application container and shares a network inter‐ face with the adapter container. The adapter container is a simple container that only contains the shell script for determining the health of the database. This script can then be set up as the health check for the database container and can perform what‐ ever rich health checks our application requires. If these checks ever fail, the database will be automatically restarted.


第2部分 - servring pattern （服务模式）
described patterns for grouping collections of containers that are scheduled on the same machine. These groups are tightly coupled, symbiotic sys‐ tems. They depend on local, shared resources like disk, network interface, or inter- process communications. Such collections of containers are important patterns, but they are also building blocks for larger systems. Reliability, scalability, and separation of concerns dictate that real-world systems are built out of many different compo‐ nents, spread across multiple machines. 

## Introduction to Microservices

Additionally, the introduction of formal APIs in between different microservices decouples the teams from one another and provides a reliable contract between the different services. This formal contract reduces the need for tight synchronization among the teams because the team providing the API understands the surface area that it needs to keep stable, and the team consuming the API can rely on a stable ser‐ vice without worrying about its details. This decoupling enables teams to independ‐ ently manage their code and release schedules, which in turn improves each team’s ability to iterate and improve their code.


As Yegge's recalls that one day Jeff Bezos issued a mandate, sometime back around 2002 (give or take a year):

All teams will henceforth expose their data and functionality through service interfaces.
Teams must communicate with each other through these interfaces.
There will be no other form of inter-process communication allowed: no direct linking, no direct reads of another team’s data store, no shared-memory model, no back-doors whatsoever. The only communication allowed is via service interface calls over the network.
It doesn’t matter what technology they use.
All service interfaces, without exception, must be designed from the ground up to be externalizable. That is to say, the team must plan and design to be able to expose the interface to developers in the outside world. No exceptions.

The two foremost disadvantages are that because the system has become more loosely coupled, debugging the system when failures occur is significantly more diffi‐ cult. You can no longer simply load a single application into a debugger and deter‐ mine what went wrong. Any errors are the byproducts of a large number of systems often running on different machines. This environment is quite challenging to repro‐ duce in a debugger. As a corollary, microservices-based systems are also difficult to design and architect. A microservices-based system uses multiple methods of com‐ municating between services; different patterns (e.g., synchronous, asynchronous, message-passing, etc.); and multiple different patterns of coordination and control among the services.

These challenges are the motivation for distributed patterns. If a microservices archi‐ tecture is made up of well-known patterns, then it is easier to design because many of the design practices are specified by the patterns. Additionally, patterns make the sys‐ tems easier to debug because they enable developers to apply lessons learned across a number of different systems that use the same patterns.

### Replicated Load-Balanced Services
The simplest distributed pattern, and one that most are familiar with, is a replicated load-balanced service. 
Examples of stateless services include things like static content servers and complex middleware systems that receive and aggre‐ gate responses from numerous different backend systems.
*When building an application for a replicated service pattern, be sure to include a special URL that implements this readiness check.

### Session Tracked Services
 Regardless of the reason, an adaption of the stateless replicated ser‐ vice pattern is to use session tracked services, which ensure that all requests for a sin‐ gle user map to the same replica, as illustrated i

 Generally speaking, this session tracking is performed by hashing the source and des‐ tination IP addresses and using that key to identify the server that should service the requests. So long as the source and destination IP addresses remain constant, all requests are sent to the same replica.

Application-Layer Replicated Services
### Introducing a Caching Layer
A cache exists between your stateless application and the end-user request. The simplest form of caching for web applications is a caching web proxy. The caching proxy is simply an HTTP server that maintains user requests in memory state. If two users request the same web page, only one request will go to your backend; the other will be serviced out of memory in the cache. This is illustra‐ ted in Figure 5-4.

For our purposes, we will use Varnish, an open source web cache.
Though this approach is simple, it has some disadvantages, namely that you will have to scale your cache at the same scale as your web servers. 

### Rate Limiting and Denial-of-Service Defense
a denial of service can come simply from a developer misconfiguring a client or a site-reliability engineer accidentally running a load test against a production installation. Thus, it makes sense to add gen‐ eral denial-of-service defense via rate limiting to the caching layer. Most HTTP reverse proxies like Varnish have capabilities along this line. In particular, Varnish has a throttle module that can be configured to provide throttling based on IP address and request path, as well as whether or not a user is logged in.

When a user hits the rate limit, the server will return the 429 error code indicating that too many requests have been issued. 

### SSL Termination
Unfortunately, the Varnish web cache can’t be used for SSL termination, but fortunately, the nginx application can. Thus we want to add a third layer to our state‐ less application pattern, which will be a replicated layer of nginx servers that will han‐ dle SSL termination for HTTPS traffic and forward traffic on to our Varnish cache. HTTP traffic continues to travel to the Varnish web cache, and Varnish forwards traf‐ fic on to our web application, as shown in Figure 5-8.

## Shared Service
Replicated services are generally used for building stateless services, whereas sharded services are generally used for building stateful services. The primary reason for sharding the data is because the size of the state is too large to be served by a single machine. Sharding enables you to scale a service in response to the size of the state that needs to be served.

the primary reason for sharding any service is to increase the size of the data being stored in the service. To understand how this helps a caching system, imagine the following system: Each c

### Sharded Caching
不是很懂

### Sharding function
Shard = ShardingFunction(Req)
Determinism is important because it ensures that a particular request R always goes to the same shard in the service. Uniformity is important because it ensures that load is evenly spread between the different shards.

### Selecting a Key
 Consequently, instead of hashing the entire request object, a much better sharding function would be shard(request.path)
 shard(country(request.ip), request.path)

### Consistent Hashing Functions
better

### Shared, Replicated Servering
Sharding is useful when considering any sort of service where there is more data than can fit on a single machine

### Hot Sharing Systems
Ideally the load on a sharded cache will be perfectly even, but in many cases this isn’t true and “hot shards” appear because organic load patterns drive more traffic to one particular shard.

Figure 6-3

# Scatter & Gather
hat replicate for scalability in terms of the number of requests processed per second (the stateless replicated pattern), as well as scalability for the size of the data (the sharded data pattern).
we introduce the scatter/gather pattern, which uses replication for scalability in terms of time. Specifi‐ cally, the scatter/gather pattern allows you to achieve parallelism in servicing requests, enabling you to service them significantly faster than you could if you had to service them sequentially.

However, in con‐ trast to replicated and sharded systems, with scatter/gather requests are simultane‐ ously farmed out to all of the replicas in the system. Each replica does a small amount of processing and then returns a fraction of the result to the root. The root server then combines the various partial results together to form a single complete response to the request and then sends this request back out to the client. 

 Scatter/gather can be seen as sharding the computation necessary to service the request, rather than sharding the data (although data sharding may be part of it as well).

 ## Scatter/Gather with Root Distribution

 ### Hands On: Distributed Document Search
 consider the task of searching across a large database of documents for all documents that contain the words “cat” and “dog.”
increased parallelization comes at a cost, and thus choosing the right number of leaf nodes in the scatter/gather pattern is critical to designing a performant distributed system.

Increased parallelism doesn’t always speed things up because of overhead on each node.
• Increased parallelism doesn’t always speed things up because of the straggler problem.
• The performance of the 99th percentile is more important than in other systems because each user request actually becomes numerous requests to the service.

### Choosing the Right Number of Leaves
The same straggler problem applies to availability. If you issue a request to 100 leaf nodes, and the probability that any leaf node failing is 1 in 100, you are again practi‐ cally guaranteed to fail every single user request.

### Scaling Scatter/Gather for Reliability and Scale
put them all together

# Functions and Event-Driven Processing
we have examined design for systems with long-running computation. The servers that handle user requests are always up and running. This pattern is the right one for many applications that are under heavy load, keep a large amount of data in memory, or require some sort of background processing. However, there is a class of applications that might only need to temporarily come into existence to handle a sin‐ gle request, or simply need to respond to a specific event. This style of request or event-driven application design has flourished recently as large-scale public cloud providers have developed function-as-a-service (FaaS) products.

More recently, FaaS implementations have also emerged running on top of cluster orchestrators in private cloud or physical environments. This chapter describes emerging architectures for this new style of computing. In many cases, FaaS is a component in a broader archi‐ tecture rather than a complete solution.

## Determining When FaaS Makes Sense
The benefits of FaaS are primarily for the developer.
FaaS forces you to strongly decouple each piece of your service. Each function is entirely independent. The only communication is across the network, and each function instance cannot have local memory, requiring all states to be stored in a storage service. This forced decoupling can improve the agility and speed with which you can develop services, but it can also significantly complicate the operations of the same service.

### The Need for Background Processing

### The Need to Hold Data in Memory

### The Costs of Sustained Request-Based Processing

## Patterns for FaaS
### The Decorator Pattern: Request or Response Transformation
A great example of the value of the decorator pattern is adding defaults to the input to an HTTP RESTFul API. In many cases in the API, there are fields whose values should have sane defaults if they are left empty. For example, you may want a field to default to true, but it’s difficult to accomplish this in classical JSON, because the default value for a field is null, which is generally understood to be false. To resolve this, we can add defaulting logic either in the front of the API server or within the application code itself (e.g., if (field == null) field = true). However, both of these solutions are somewhat unappealing since the defaulting mechanism is fairly conceptually independent from the handling of the request. Instead, we can use the FaaS decorator pattern to transform the request in between the user and the service implementation.

### Handling Events
 Events, as I see them, instead tend to be single-instance and asynchronous in nature. Events are important and need to be properly handled, but they are fired off from a main interaction and responded to some time later. Examples of events include a user sign‐ ing up for a new service (which might trigger a welcome email, someone uploading a file to a shared folder (which might send notifications to everyone who has access to the folder), or even a machine being about to reboot (which might notify an operator or automated system to take appropriate action).
 A concrete example of integrating an event-based component to an existing service is implementing two-factor authentication. In this case, the event is the user logging into a service. The service can generate an event for this action, fire it into a function- based handler that takes the code and the user’s contact information, and sends the two-factor code via text message.

 ### Event-Based Pipelines
 For example, while it is difficult to see how a human approving a ticket in a ticketing system like Jira could be integrated into a microservices application, it’s quite easy to see how that event could be incorporated into a event-driven pipeline.

 As an example of this, imagine a pipeline in which the original event is code being submitted into a source control system. This event then triggers a build. The build may take multiple minutes to complete, and when it does, it fires an event to a build analysis function. This function takes different actions if the build is successful or fails. If the build succeeded, a ticket is created for a human to approve it to be pushed to production. Once the ticket is closed, the act of closing is an event that triggers the actual push to production. If the build failed, a bug is filed on the failure, and the event pipeline terminates.

 # Ownership Election
 how you scale assignment. In many different systems, there is a notion of ownership where a specific process owns a specific task. 

 ## Determining If You Even Need Master Election

 ## The Basics of Master Election

 ### Implementing Locks
 When using distributed locks, it is critical to ensure that any pro‐ cessing you do doesn’t last longer than the TTL of the lock. One good practice is to set a watchdog timer when you acquire the lock. The watchdog contains an assertion that will crash your program if the TTL of the lock expires before you have called unlock.

 # Batch Computational Patterns
 Examples of a batch process include generating aggregation of user telemetry data, analyzing sales data for daily or weekly reporting, or transcoding video files. Batch processes are generally characterized by the need to process large amounts of data quickly using parallelism to speed up the processing. The most famous pattern for distributed batch processing is the MapReduce pattern, which has become an entire industry in itself. However, there are several other patterns that are useful for batch processing, which are described in the following chapters.
 
 ## Work Queue Systems
 figure 10.1


You might wonder why we include a v1 in the API definition. Will there ever be a v2 of this interface? It may not seem logical, but it costs very little to version your API when you initially define it. Refactoring versioning onto an API without it, on the other hand, is very expensive. Consequently, it is a best practice to always add versions to your APIs even if you’re not sure they will ever change. Better safe than sorry.

### Hands On: Implementing a Video Thumbnailer

### The Multi-Worker Pattern

# Event-Driven Batch Processing


## Patterns of Event-Driven Processing

### Copier

### Filter

### Splitter
An example of an application of the splitter pattern is processing online orders where people can receive shipping notifications either by email or text message. Given a work queue of items that have been shipped, the splitter divides it into two different queues: one that is responsible for sending emails and another devoted to sending text messages. 

### Merger

## Publisher/Subscriber infrastructure
how installing Kafka into your Kubernetes cluster can dra‐ matically simplify the task of building a work queue based system.

## Coorindated Batch Proceesing

### Join (or Barrier Synchronization)
### Reduce
### Sum

### Hands On: An Image Tagging & Processing Pipeline
To see how coordinated batch processing can be used to accomplish a larger batch task, consider the job of tagging and processing a set of images. Let us assume that we have a large collection of images of highways at rush hour, and we want to count both the numbers of cars, trucks, and motorcycles, as well as distribution of the colors of each of the cars. Let us also suppose that there is a preliminary step to blur the license plates of all of the cars to preserve anonymity.





