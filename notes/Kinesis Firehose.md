---
publish: true
---
🎥 [Better Dev](https://www.youtube.com/watch?v=DPT3swb6zgI)
# What is it
- delivers streaming event data from data producers (e.g. IoT, events) to a durable store for analytics (e.g. S3, Redshift, Elastisearch, Splunk logs) that will end up in BI tools/dashboards 
- managed service -- elastic scaling that responses to increase
- batches events into a single output file (more efficient)
![Excalidraw/Drawing 2023-05-12 kinesis firehose example.excalidraw=StopAfGm](../images/Drawing%202023-05-12%20kinesis%20firehose%20example.svg.md#^group=StopAfGm)
Best practices
- buffer size (size of file with batched transactions) & buffer interval (time based)
- 1 MB/s default PUT limit to firehose, configurable
- compress files before delivering to storage like S3

![Drawing 2023-05-12 kinesis firehose example.excalidraw](../images/Drawing%202023-05-12%20kinesis%20firehose%20example.svg)

When to use Firehose to deliver data from Kinesis data stream?
- when data needs to be transformed or preprocessed before delivering to another AWS service or stage destination
	- transform: compress, encrypt, convert
	- optimized to deliver to AWS destinations S3, Reshift, Elasticsearch
- when you want scaling capabilities based on throughput of incoming data
	- retries, buffering, error handling
- stream-to-stream transformation & connection
	- firehose as intermediary step between two Kinesis data streams
![Drawing 2023-05-12 kinesis data stream.excalidraw](../images/Drawing%202023-05-12%20kinesis%20data%20stream.svg)

Kinesis Client Library (KCL)
- Java based library used in apps to consume data *from* kinesis streams