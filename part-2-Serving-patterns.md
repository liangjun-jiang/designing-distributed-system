第2部分 - servring pattern （服务模式）

Reliability, scalability, and separation of concerns dictate that real-world systems are built out of many different components, spread across multiple machines. 

## Introduction to Microservices
Recently, the term microservices has become a buzzword for describing multi-node distributed software architectures. Microservices describe a system built out of many different components running in different processes and communicating over defined APIs. [Microservices stand in contrast to monolithic systems, which tend to place all of the functionality for a service within a single, tightly coordinated application.](FigureII-1) 


There are numerous benefits to the microservices approach, most of them are centered around reliability and agility. Microservices break down an application into small pieces, each focused on providing a single service. This reduced scope enables each service to be built and maintained by a single “two pizza” team. Reduced team size also reduces the overhead associated with keeping a team focused and moving in one direction.

Additionally, the introduction of formal APIs in between different microservices decouples the teams from one another and provides a reliable contract between the different services. This formal contract reduces the need for tight synchronization among the teams because the team providing the API understands the surface area that it needs to keep stable, and the team consuming the API can rely on a stable ser‐ vice without worrying about its details. This decoupling enables teams to independ‐ ently manage their code and release schedules, which in turn improves each team’s ability to iterate and improve their code.



The two foremost disadvantages are that because the system has become more loosely coupled, debugging the system when failures occur is significantly more difficult. You can no longer simply load a single application into a debugger and determine what went wrong. Any errors are the byproducts of a large number of systems often running on different machines. This environment is quite challenging to repro‐ duce in a debugger. As a corollary, microservices-based systems are also difficult to design and architect. A microservices-based system uses multiple methods of com‐ municating between services; different patterns (e.g., synchronous, asynchronous, message-passing, etc.); and multiple different patterns of coordination and control among the services.

These challenges are the motivation for distributed patterns. If a microservices architecture is made up of well-known patterns, then it is easier to design because many of the design practices are specified by the patterns. Additionally, patterns make the sys‐ tems easier to debug because they enable developers to apply lessons learned across a number of different systems that use the same patterns.

## Chapter 5 Replicated Load-Balanced Services
The simplest distributed pattern, and one that most are familiar with, is a replicated load-balanced service. 
Examples of stateless services include things like static content servers and complex middleware systems that receive and aggregate responses from [numerous different backend systems](Figure 5-1).
*When building an application for a replicated service pattern, be sure to include a special URL that implements this readiness check.
Hands-on: Creating a Replicated Service in Kubernetes

### Session Tracked Services
 Regardless of the reason, an adaption of the stateless replicated service pattern is to use session tracked services, which ensure that all requests for a single user map to the same replica, as illustrated [Figure 5-3]()

 Generally speaking, this session tracking is performed by hashing the source and destination IP addresses and using that key to identify the server that should service the requests. So long as the source and destination IP addresses remain constant, all requests are sent to the same replica.

## Application-Layer Replicated Services
many applications use HTTP as the protocol for speaking with each other, and knowledge of the appli‐ cation protocol that is being spoken enables further refinements to the replicated stateless serving pattern for additional functionality.

### Introducing a Caching Layer
A cache exists between your stateless application and the end-user request. The simplest form of caching for web applications is a caching web proxy. The caching proxy is simply an HTTP server that maintains user requests in memory state. If two users request the same web page, only one request will go to your backend; the other will be serviced out of memory in the cache. This is illustrated in [Figure 5-4]().

For our purposes, we will use [Varnish](https://varnish-cache.org/), an open source web cache.

[Figure 5-6]
### Rate Limiting and Denial-of-Service Defense
Few of us build sites with the expectation that we will encounter a denial-of-service attack. But as more and more of us build APIs, a denial of service can come simply from a developer misconfiguring a client or a site-reliability engineer accidentally running a load test against a production installation. Thus, it makes sense to add gen‐ eral denial-of-service defense via rate limiting to the caching layer. Most HTTP reverse proxies like Varnish have capabilities along this line. In particular, Varnish has a throttle module that can be configured to provide throttling based on IP address and request path, as well as whether or not a user is logged in.

When a user hits the rate limit, the server will return the 429 error code indicating that too many requests have been issued. 

### SSL Termination
Unfortunately, the Varnish web cache can’t be used for SSL termination, but fortunately, the nginx application can. Thus we want to add a third layer to our state‐ less application pattern, which will be a replicated layer of nginx servers that will han‐ dle SSL termination for HTTPS traffic and forward traffic on to our Varnish cache. HTTP traffic continues to travel to the Varnish web cache, and Varnish forwards traf‐ fic on to our web application, as shown in [Figure 5-8]().

Hands On: Deploying nginx and SSL Termination

## Chapter 6 Shared Service
Replicated services are generally used for building stateless services, whereas sharded services are generally used for building stateful services. The primary reason for sharding the data is because the size of the state is too large to be served by a single machine. Sharding enables you to scale a service in response to the size of the state that needs to be served.
[Figure 6-1. Replicated service versus sharded service]()

### Sharded Caching
Hands On: Deploying an Ambassador and Memcache for a Sharded Cache

### Sharding function
Hands On: Building a Consistent HTTP Sharding Proxy


### Shared, Replicated Servering
Sharding is useful when considering any sort of service where there is more data than can fit on a single machine

### Hot Sharing Systems
Ideally the load on a sharded cache will be perfectly even, but in many cases this isn’t true and “hot shards” appear because organic load patterns drive more traffic to one particular shard.

[Figure 6-3]()

# Chapter 7 Scatter & Gather
we introduce the scatter/gather pattern, which uses replication for scalability in terms of time. Specifi‐ cally, the scatter/gather pattern allows you to achieve parallelism in servicing requests, enabling you to service them significantly faster than you could if you had to service them sequentially.

However, in contrast to replicated and sharded systems, with scatter/gather requests are simultaneously farmed out to all of the replicas in the system. Each replica does a small amount of processing and then returns a fraction of the result to the root. The root server then combines the various partial results together to form a single complete response to the request and then sends this request back out to the client. 

 Scatter/gather can be seen as sharding the computation necessary to service the request, rather than sharding the data (although data sharding may be part of it as well).[Figure 7-1]()

 ## Scatter/Gather with Root Distribution
[Figure 7-2](Example of a term-sharded scatter/gather system)

## Scatter/Gather with Leaf Sharding
As a concrete example of this sort of architecture, consider implementing search across a very large document set (all patents in the world, for example); in such a case, the data is too large to fit in the memory of a single machine, so instead the data is sharded across multiple replicas.
[Figure 7-3]()
Hands On: Distributed Document Search
 

# Chapter 8 Functions and Event-Driven Processing
There is a class of applications that might only need to temporarily come into existence to handle a sin‐ gle request, or simply need to respond to a specific event. This style of request or event-driven application design has flourished recently as large-scale public cloud providers have developed function-as-a-service (FaaS) products.

More recently, FaaS implementations have also emerged running on top of cluster orchestrators in private cloud or physical environments. This chapter describes emerging architectures for this new style of computing. In many cases, FaaS is a component in a broader architecture rather than a complete solution.

## Determining When FaaS Makes Sense
The benefits of FaaS are primarily for the developer.
FaaS forces you to strongly decouple each piece of your service. Each function is entirely independent. The only communication is across the network, and each function instance cannot have local memory, requiring all states to be stored in a storage service. This forced decoupling can improve the agility and speed with which you can develop services, but it can also significantly complicate the operations of the same service.

### The Need for Background Processing

### The Need to Hold Data in Memory

### The Costs of Sustained Request-Based Processing

## Patterns for FaaS
### The Decorator Pattern: Request or Response Transformation
A great example of the value of the decorator pattern is adding defaults to the input to an HTTP RESTFul API. In many cases in the API, there are fields whose values should have sane defaults if they are left empty. For example, you may want a field to default to true, but it’s difficult to accomplish this in classical JSON, because the default value for a field is null, which is generally understood to be false. To resolve this, we can add defaulting logic either in the front of the API server or within the application code itself (e.g., if (field == null) field = true). However, both of these solutions are somewhat unappealing since the defaulting mechanism is fairly conceptually independent from the handling of the request. Instead, we can use the FaaS decorator pattern to transform the request in between the user and the service implementation.
[Figure 8-1]  The decorator pattern applied to HTTP APIs

### Handling Events
 Events, as I see them, instead tend to be single-instance and asynchronous in nature. Events are important and need to be properly handled, but they are fired off from a main interaction and responded to some time later. Examples of events include a user sign‐ ing up for a new service (which might trigger a welcome email, someone uploading a file to a shared folder (which might send notifications to everyone who has access to the folder), or even a machine being about to reboot (which might notify an operator or automated system to take appropriate action).
 A concrete example of integrating an event-based component to an existing service is implementing two-factor authentication. In this case, the event is the user logging into a service. The service can generate an event for this action, fire it into a function- based handler that takes the code and the user’s contact information, and sends the two-factor code via text message.

 ### Event-Based Pipelines
 For example, while it is difficult to see how a human approving a ticket in a ticketing system like Jira could be integrated into a microservices application, it’s quite easy to see how that event could be incorporated into a event-driven pipeline.

 As an example of this, imagine a pipeline in which the original event is code being submitted into a source control system. This event then triggers a build. The build may take multiple minutes to complete, and when it does, it fires an event to a build analysis function. This function takes different actions if the build is successful or fails. If the build succeeded, a ticket is created for a human to approve it to be pushed to production. Once the ticket is closed, the act of closing is an event that triggers the actual push to production. If the build failed, a bug is filed on the failure, and the event pipeline terminates.

 # Chapter 9 Ownership Election
 In the context of a single server, ownership is generally straightforward to achieve because there is only a single application that is establishing ownership, and it can use well-established in-process locks to ensure that only a single actor owns a particular shard or context. However, restricting ownership to a single application limits scala‐ bility, since the task can’t be replicated, and reliability, since if the task fails, it is unavailable for a period of time. Consequently, when ownership is required in your system, you need to develop a distributed system for establishing ownership.
 In the diagram, there are three replicas that could be the owner or master. Initially, the first replica is the master. Then that replica fails, and replica number three then becomes the master. Finally, replica number one recovers and returns to the group, but replica three remains as the master/owner.
 A general diagram of distributed ownership is shown in [Figure 9-1]()

 