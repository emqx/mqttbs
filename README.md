# MQTT Benchmark Suite Specification
## Introduction

MQTT is a communication protocol based on Publish/Subscribe model. It has the following features:

- Simple and easy to implement
- QoS support for complex device network environment.
- Lightweight and designed specially for unstable network connections and bandwidth saving
- Data irrelevant
- Continuous session awareness

Those characteristics make it suited for IoT applications, and become one of the most popular protocols in IoT domain.

In a typical MQTT Publish/Subscribe architecture, there are publishers, subscribers, and a broker in the system. A publisher sends a message to the broker on a specific topic and then the broker sends that message to all subscribers which are subscribed to the same topic.

![](_assets/mqtt_model.png)

As a broker is the central component in an MQTT based IoT application, it plays an important role in the scalability and availability of the application.  There have been many research papers and technical writeups on the performance evaluation and comparison of various MQTT brokers. However, the existing benchmarks often measure particular factors only, with scenarios not matching the realistic large-scale IoT applications. Through development and customer support experience of the past decade, we have learnt that many factors impact MQTT broker performance.

For one thing, MQTT protocol states many features improving reliability and security, which bring overhead cost on broker side as well. Some of the features are:

- QoS
- Keep Alive
- Clean Session
- Retain
- Topic wildcards
- Authentication method
- TLS authentication enablement

In addition, different MQTT versions suggest different functions. For example, shared subscription is supported in MQTT 5.0 but not 3.1.1 or 3.1.

For another thing, MQTT brokers deployment ways influence the performance evaluation. The brokers can be deployed on single node, or in a cluster way. If it is deployed in a cluster way, it can be put behind a load balancer or not. To be comparable, the brokers should be deployed in the similar ways.

What is more, environment conditions like hardware, OS, system and network configuration also impact the evaluation result. Some of the important conditions are:

- receive buffer size
- network round trip time

The rapid development of IoT applications brings new challenges. As a hub of devices and data, the capability of the MQTT broker to support massive device connectivity and data throughput is critical to the IoT business. Consequently, a thorough evaluation on its performance is important in the full IoT solution. The intent of this work is suggesting metrics and evaluation criteria, presenting real-world scenarios and use cases, and in the future, providing a tool that fully compatible with the benchmark suite. To this end, we hope to establish a practical base for benchmarking MQTT brokers, iteratively optimize its coverage, and help users find the most suitable MQTT broker for their needs and workloads.

## Methodology

To measure performance of an MQTT broker, we need to define metrics,  which can be looked at in a variety of use cases. Two primary  perspectives of measurement are considered. One focuses on the computing resources perspective. The other focuses on the capabilities provided  by the MQTT broker itself, and divides into three detailed aspects -  scalability, availability, and latency, as suggested in Google “Cloud  Pub/Sub” product guide. It should be noticed that different aspects may  contradict each other under different circumstances. To achieve  different evaluation purposes, user needs to pick the suitable metrics.

<table>
	<tr>
		<th>test</th>
	</tr>

</table>

A benchmark tool then is used to simulate publishers and/or subscribers based on use cases. It first generates dataset for benchmark, and loads the dataset to construct a series of MQTT requests, then executes to perform publishing and subscribing. The tool should work in a consistent way regardless of what the MQTT broker is. As many practical use cases involve large number of publishers/subscribers, the tool should also be implemented in a sophisticated way in order to generate desired large-scale load.

As part of the benchmark submisison, a full disclosure report is suggested. The intent of the report is to simplify comparison between results of different brokers, and to provide enough information so other users can replicate the results of this benchmark. The report should identify the benchmark tool it uses, explain the detailed use cases, and list the hardware, OS and network conditions to perform the benchmark. It should also cover comparison data regarding the measurement metrics, both including computing resources perspective and broker capability perspective.

## Scenarios and use cases

By analyzing realistic requirements from our customers, we categorize the benchmark into the following scenarios:

- Connection
- Fan-out
- Point-to-point
- Fan-in
- Composite

### Connection

In a connection scenario, a batch of clients connect to the broker within a period of time, and keep the connections with the broker for quite a while. Each client subscribes to a topic once the connections established.

A use case for this scenario is presented as:
<table>
	<tr>
		<td>Use case</td>
		<td>simultaneous-conn-ssl-auth-1M-100K</td>
	</tr>
	<tr>
		<td>Description</td>
		<td>1,000,000 simultaneous subscribers connect to broker using SSL/TLS</td>
	</tr>
	<tr>
		<td>Details</td>
		<td>
			<ol>
				<li>Broker is enabled with SSL/TLS, and configured to use user/password as authentication method.</li>
				<li>1,000,000 clients connect to the broker within 400 seconds. Each client connects to the SSL port of the broker, using MQTT 3.1.1 protocol and user/password authentication method. The Keep Alive property is set as 150, and Clean Session is set as 1.</li>
				<li>Once a client is connected, it subscribes to a topic using QoS 0 following the rule that every 10 client subscribes to the same topic. As a result, all 1,000,000 clients subscribe to 100,000 topics.</li>
				<li>Keep all clients online for 30 minutes.</li>
			</ol>
		</td>
	</tr>
	<tr>
		<td>Computing resource metrics of interest</td>
		<td>
			<ul>
				<li>CPU load and usage</li>
				<li>Memory usage</li>
				<li>Disk I/O rate</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td>Broker capability metrics of interest</td>
		<td>
			<ul>
				<li>Rate of new established connections</li>
				<li>Number of concurrent connections</li>
				<li>Number of topics</li>
				<li>Success rate</li>
			</ul>
		</td>
	</tr>
</table>

