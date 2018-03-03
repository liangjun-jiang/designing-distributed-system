第2部分 - servring pattern （服务模式）
described patterns for grouping collections of containers that are scheduled on the same machine. These groups are tightly coupled, symbiotic sys‐ tems. They depend on local, shared resources like disk, network interface, or inter- process communications. Such collections of containers are important patterns, but they are also building blocks for larger systems. Reliability, scalability, and separation of concerns dictate that real-world systems are built out of many different compo‐ nents, spread across multiple machines. 

## Introduction to Microservices

Additionally, the introduction of formal APIs in between different microservices decouples the teams from one another and provides a reliable contract between the different services. This formal contract reduces the need for tight synchronization among the teams because the team providing the API understands the surface area that it needs to keep stable, and the team consuming the API can rely on a stable ser‐ vice without worrying about its details. This decoupling enables teams to independ‐ ently manage their code and release schedules, which in turn improves each team’s ability to iterate and improve their code.



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