## 单节点模式 (Single-Node Pattern)
### Sidecar模式
The sidecar pattern is a single- node pattern made up of two containers. The first is the application container. It con‐ tains the core logic for the application. Without this container, the application would not exist. In addition to the application container, there is a sidecar container. The role of the sidecar is to augment and improve the application container, often without the application container’s knowledge. In its simplest form, a sidecar container can be used to add functionality to a container that might otherwise be difficult to improve.

[Figure 2-1](https://i.imgur.com/3RIql0S.png)
比如dynamic configuration with Sidecars
比如Sync with Git

#### An Example Sidecar: Adding HTTPS to a Legacy Service
[Figure 2-2]()
1. 比如hpm
2. 比如ngrok
### Dynamic Con guration with Sidecars
Many applications use a configuration file for parameterizing the application; this may be a raw text file or something more structured like XML, JSON, or YAML. Many pre-existing applica‐ tions were written to assume that this file was present on the filesystem and read their configuration from there. However, in a cloud-native environment it is often quite useful to use an API for updating configuration. This allows you to do a dynamic push of configuration information via an API instead of manually logging in to every server and updating the configuration file using imperative commands. The desire for such an API is driven both by ease of use as well as the ability to add automation like rollback, which makes configuring (and reconfiguring) safer and easier.
[Figure 2-3]()

1. Paas - 管理开发机

### Modular Application Containers
Implement an HTTP /topz interface that provides a readout of resource usage. such as [Top-like interface for container metrics](https://github.com/bcicen/ctop)

this topz functionality can be deployed as a sidecar container that shares the process-id (PID) namespace with the application container. This topz container can introspect all running processes and provide a consistent user interface. Moreover, you can use the orchestration system to automatically add this container to all applications deployed via the orchestration system to ensure that there is a consistent set of tools available for all applications running in your infrastructure.
### Designing Sidecars for Modularity and Reusability

In particular, you need to focus on developing three areas:
* Parameterizing your containers
* Creating the API surface of your container
* Documenting the operation of your container





## Ambassador 模式
the ambassador pattern, where an ambassador container brokers interactions between the application container and the rest of the world. As with other single-node patterns, the two con‐ tainers are tightly linked in a symbiotic pairing that is scheduled to a single machine. A canonical diagram of this pattern is shown in Figure 3-1.
[Figure 3-1 Generic ambassador pattern]()



### Using an Ambassador to Shard a Service
[read more to understand argument in this part]
[Figue 3-2 A generic shared service]()
Sometimes the data that you want to store in a storage layer becomes too big for a single machine to handle. In such situations, you need to shard your storage layer. Sharding splits up the layer into multiple disjoint pieces, each hosted by a separate machine. 
The following section describes how to use the single-node ambassador pattern for client-side sharding.Sharding and a multi-node sharded service design pattern are discussed in great detail in Chapter 6
For example, Implementing a Sharded Redis, an off-the-shelf open source implementation can be used for the ambassador, speeding up the development of the overall distributed system.


### Using an Ambassador for Service Brokering
Imagine a front‐ end that relies on a MySQL database to store its data. In the public cloud, this MySQL service might be provided as software-as-a-service (SaaS), whereas in a private cloud it might be necessary to dynamically spin up a new virtual machine or container run‐ ning MySQL.
Consequently, building a portable application requires that the application know how to introspect its environment and find the appropriate MySQL service to connect to. This process is called service discovery, and the system that performs this discovery and linking is commonly called a service broker.

### Using an Ambassador to Do Experimentation or Request Splitting
In many production systems, it is advantageous to be able to perform request splitting, where some fraction of all requests are not serv‐ iced by the main production service but rather are redirected to a different imple‐ mentation of the service. Most often, this is used to perform experiments with new, beta versions of the service to determine if the new version of the software is reliable or comparable in performance to the currently deployed version.

As before, the application container simply connects to the service on localhost, while the ambassador container receives the requests, proxies responses to both the production and experimental systems, and then returns the production responses back as if it had performed the work itself.

For example, using Nginx to implementing 10% experiments

在金服内的用例： api 分流， mobilegw （无线网关）？？？


## Adapter 模式
In the adapter pattern, the adapter container is used to modify the interface of the application container so that it conforms to some predefined interface that is expected of all applications. For example, an adapter might ensure that an application implements a consistent monitoring interface. Or it might ensure that log files are always written to stdout or any number of other conventions.
[Figure 4-1 The generic adapater pattern]()

### Monitoring 
When monitoring your software, you want a single solution that can automatically discover and monitor any application that is deployed into your environment. To make this feasible, every application has to implement the same monitoring interface. There are numerous examples of standardized monitoring interfaces, such as syslog, event tracing on Windows (etw), JMX for Java applications, and many, many other protocols and interfaces. However, each of these is unique in both protocol for com‐ munication as well as the style of communication (push versus pull).
Sadly, applications in your distributed system are likely to span the gamut from code that you have written yourself to off-the-shelf open source components. As a result, you will find yourself with a wide range of different monitoring interfaces that you need to integrate into a single well-understood system.
Applying the adapter pattern to monitoring, we see that the application container is simply the application that we want to monitor. The adapter container contains the tools for transforming the monitoring interface exposed by the application container into the interface expected by the general- purpose monitoring system.
Decoupling the system in this fashion makes for a more comprehensible, maintaina‐ ble system. Rolling out new versions of the application doesn’t require a rollout of the monitoring adapter. Additionally, the monitoring container can be reused with multi‐ ple different application containers. The monitoring container may even have been supplied by the monitoring system maintainers independent of the application devel‐ opers. Finally, deploying the monitoring adapter as a separate container ensures that each container gets its own dedicated resources in terms of both CPU and memory.
 32 | Chapter 4: Adapters
This ensures that a misbehaving monitoring adapter cannot cause problems with a user-facing service.

For example, use [Prometheus](https://prometheus.io) for monitoring

### Logging
Systems might divide their logs into different levels (such as debug, info, warning, and error) with each level going into a different file. Some might simply log to stdout and stderr. This is especially problematic in the world of containerized applications where there is a general expectation that your containers will log to stdout, because that is what is available via commands like docker logs or kubectl logs.

the adapter pattern can help provide a modular, re- usable design for both of these situations. While the application container may log to a file, the adapter container can redirect that file to stdout. Different application con‐ tainers can log information in different formats, but the adapter container can trans‐ form that data into a single structured representation that can be consumed by your log aggregator. Again, the adapter is taking a heterogeneous world of applications and creating a homogenous world of common interfaces.

For example: use [Fluentd](https://fluentd.org) to normalize Different Logging Formats  

### Adding a Health Monitor
Consider the task of monitoring the health of an off-the-shelf database container.
The database runs in the application container and shares a network interface with the adapter container. The adapter container is a simple container that only contains the shell script for determining the health of the database. This script can then be set up as the health check for the database container and can perform what‐ ever rich health checks our application requires. If these checks ever fail, the database will be automatically restarted.

Keep this in mind, *Sometimes design patterns aren’t just for the developers who apply them, but lead to the development of communities that can collaborate and share solutions between members of the community as well as the broader developer ecosystem.*