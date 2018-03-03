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
### Sharder

### Merger
### Hands On: Building an Event-Driven flow for New User Sign-Up


## Publisher/Subscriber infrastructure
how installing Kafka into your Kubernetes cluster can dra‐ matically simplify the task of building a work queue based system.

### Hands On :Deploying Kafka


## Coorindated Batch Proceesing

### Join (or Barrier Synchronization)
### Reduce
### Sum

### Hands On: An Image Tagging & Processing Pipeline
To see how coordinated batch processing can be used to accomplish a larger batch task, consider the job of tagging and processing a set of images. Let us assume that we have a large collection of images of highways at rush hour, and we want to count both the numbers of cars, trucks, and motorcycles, as well as distribution of the colors of each of the cars. Let us also suppose that there is a preliminary step to blur the license plates of all of the cars to preserve anonymity.
