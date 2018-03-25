# Batch Computational Patterns
 Examples of a batch process include generating aggregation of user telemetry data, analyzing sales data for daily or weekly reporting, or transcoding video files. Batch processes are generally characterized by the need to process large amounts of data quickly using parallelism to speed up the processing. The most famous pattern for distributed batch processing is the MapReduce pattern, which has become an entire industry in itself. However, there are several other patterns that are useful for batch processing, which are described in the following chapters.
 
 ## Chapter 10 Work Queue Systems
 The simplest form of batch processing is a work queue. In a work queue system, there is a batch of work to be performed. Each piece of work is wholly independent of the other and can be processed without any interactions. 

 Figure 10-1. A generic work queueFigure 10.1

### Hands On: Implementing a Video Thumbnailer

### The Multi-Worker Pattern
These sort of event-driven processing systems are often called work ow systems, since there is a flow of work through a directed, acyclic graph that describes the vari‐ ous stages and their coordination. A basic illustration of such a system is shown in [Figure 11-1. Event-Driven Batch Processing]()


## Patterns of Event-Driven Processing
Involve the coordination of multiple different queues or the modification of the output of one or more work queues.

### Copier
The first pattern for coordinating work queues is a copier. The job of a copier is to take a single stream of work items and duplicate it out into two or more identical streams. This pattern is useful when there are multiple different pieces of work to be done on the same work item. [Figure 11-2] The copier batch pattern for transcoding

### Filter
 The role of a filter is to reduce a stream of work items to a smaller stream of work items by filtering out work items that don’t meet particular criteria. As an example of this, consider setting up a batch workflow that handles new users signing up for a service. Some set of those users will have ticked the checkbox that indicates that they wish to be contacted via email for promotions and other information. In such a workflow, you can filter the set of newly signed-up users to only be those who have explicitly opted into being contacted.
 [Figure 11-3] An example of a  lter pattern that removes all odd-numbered work items

### Splitter
An example of an application of the splitter pattern is processing online orders where people can receive shipping notifications either by email or text message. Given a work queue of items that have been shipped, the splitter divides it into two different queues: one that is responsible for sending emails and another devoted to sending text messages. 
[Figure 11-4] An example of the batch splitter pattern splitting shipping noti cations into two di erent queues

### Sharder
The role of a sharder in a workflow is to divide up a single queue into an evenly divided collection of work items based upon some sort of shard‐ ing function. There are several different reasons why you might consider sharding your workflow. One of the first is for reliability. If you shard your work queue, then the failure of a single workflow due to a bad update, infrastructure failure, or other problem only affects a fraction of your service.

### Merger
the job of a merger is to take two different work queues and turn them into a single work queue. 
[Figure 11-7]
### Hands On: Building an Event-Driven flow for New User Sign-Up


## Publisher/Subscriber infrastructure
A pub/sub API allows a user to define a collection of queues (sometimes called topics). One or more publishers publishes messages to these queues. Likewise, one or more subscribers is listening to these queues for new messages. When a message is published, it is reliably stored by the queue and subse‐ quently delivered to subscribers in a reliable manner.
how installing Kafka into your Kubernetes cluster can dramatically simplify the task of building a work queue based system.

# Conclusion: A New Beginning?




