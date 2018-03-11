## 单节点模式 (Single-Node Pattern)
### Sidecar模式
比如hpm
比如ngrok
比如dynamic configuration with Sidecars
比如Sync with Git

## Ambassador 模式
### Using an Ambassador to Shard a Service
在金服内的用例： api 分流， mobilegw （无线网关）？？？

The following section describes how to use the single-node ambassador pattern for client-side sharding.

Hands On: Implementing a Sharded Redis

### Using an Ambassador for Service Brokering
To understand what this means, imagine a front‐ end that relies on a MySQL database to store its data. In the public cloud, this MySQL service might be provided as software-as-a-service (SaaS), whereas in a private cloud it might be necessary to dynamically spin up a new virtual machine or container run‐ ning MySQL.
Consequently, building a portable application requires that the application know how to introspect its environment and find the appropriate MySQL service to connect to. This process is called service discovery, and the system that performs this discovery and linking is commonly called a service broker.

### Using an Ambassador to Do Experimentation or Request Splitting

## Adapter 模式
In the adapter pattern, the adapter container is used to modify the interface of the application container so that it conforms to some predefined interface that is expected of all applications. For example, an adapter might ensure that an application implements a consistent monitoring interface. Or it might ensure that log files are always written to stdout or any number of other conventions.
### Monitoring 
Applying the adapter pattern to monitoring, we see that the application container is simply the application that we want to monitor. The adapter container contains the tools for transforming the monitoring interface exposed by the application container into the interface expected by the general- purpose monitoring system.

### Logging
Systems might divide their logs into different levels (such as debug, info, warning, and error) with each level going into a different file. Some might simply log to stdout and stderr. This is especially problematic in the world of containerized applications where there is a general expectation that your containers will log to stdout, because that is what is available via commands like docker logs or kubectl logs.

the adapter pattern can help provide a modular, re- usable design for both of these situations. While the application container may log to a file, the adapter container can redirect that file to stdout. Different application con‐ tainers can log information in different formats, but the adapter container can trans‐ form that data into a single structured representation that can be consumed by your log aggregator. Again, the adapter is taking a heterogeneous world of applications and creating a homogenous world of common interfaces.

Hands On: Normalizing Di erent Logging Formats with Fluentd

### Adding a Health Monitor
Consider the task of monitoring the health of an off-the-shelf database container.
The database runs in the application container and shares a network interface with the adapter container. The adapter container is a simple container that only contains the shell script for determining the health of the database. This script can then be set up as the health check for the database container and can perform what‐ ever rich health checks our application requires. If these checks ever fail, the database will be automatically restarted.