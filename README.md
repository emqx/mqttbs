# Open MQTT Benchmark Suite
## Introduction

MQTT is a communication protocol based on Publish/Subscribe model. It has the following features:

- Simple and easy to implement
- QoS support for complex device network environment.
- Lightweight and designed specially for unstable network connections and bandwidth saving
- Data irrelevant
- Continuous session awareness

Those characteristics make it suited for IoT applications, and become one of the most popular protocols in IoT domain.

In a typical MQTT Publish/Subscribe architecture, there are publishers, subscribers, and a broker in the system. A publisher sends a message to the broker on a specific topic and then the broker sends that message to all subscribers which are subscribed to the same topic.

![mqtt-model](_assets/mqtt_model.png)

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
		<th rowspan="2" colspan="4">Computing resources metrics</th>
		<th colspan="12">Broker capability metrics</th>
	</tr>
	<tr>
		<td colspan="8">
			<p><b>Scalability</b></p>
			<p>the ability to handle increases in load</p>
		</td>
		<td colspan="2">
			<p><b>Availability</b></p>
			<p>the ability to handle problems and failures</p>
		</td>
		<td colspan="2">
			<p><b>Latency</b></p>
			<p>the amount of time to deliver a published message to a subscriber</p>
		</td>
	</tr>
	<tr>
		<td>CPU load and usage</td>
		<td>Memory usage</td>
		<td>Disk I/O rate</td>
		<td>Packets receiving and sending rate</td>
		<td>Rate of new established connections</td>
		<td>Number of concurrent connections</td>
		<td>Number of publishers</td>
		<td>Number of subscribers</td>
		<td>Number of topics</td>
		<td>Size of messages</td>
		<td>Rate of messages published</td>
		<td>Rate of messages subscribed</td>
		<td>Success rate</td>
		<td>Reconnection delay</td>
		<td>Average latency time</td>
		<td>90th percentile latency time</td>
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

### Fan-out

In a fan-out scenario, a large number of clients act as subscribers, with only a few or a single publisher.

![fan-out](_assets/fanout.png)

A use case for this scenario is presented as:
<table>
	<tr>
		<td>Use case</td>
		<td>fanout-5-500K</td>
	</tr>
	<tr>
		<td>Description</td>
		<td>50 publishers publish messages to 5 topics which are subscribed by 1,000 subscribers</td>
	</tr>
	<tr>
		<td>Details</td>
		<td>
			<ol>
				<li>1,050 clients are divided into 5 groups. Each group has its own distinct topic, 10 publishers and 200 subscribers.</li>
				<li>All publishers and subscribers connect to the TCP port of the broker, using MQTT 3.1.1 protocol. The Keep Alive property is set as 300, and Clean Session is set as 1.</li>
				<li>Once the connection of a subscriber is established, the subscriber immediately subscribes to the topic of that group using QoS 1.</li>
				<li>When all the connections are established, publishers of each group publish message to the topic of that group using QoS 1 with Retain as 0. The publish rate for each publisher is 50 message per second. The payload size of each message is 1 KB.</li>
				<li>Keep the publish and subscribe for 30 minutes. The expected total publish rate is 2,500 messages per second, and the expected total subscribe rate is 500,000 messages per second.</li>
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
				<li>Packets receiving and sending rate</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td>Broker capability metrics of interest</td>
		<td>
			<ul>
				<li>Number of publishers</li>
				<li>Number of subscribers</li>
				<li>Number of topics</li>
				<li>Size of messages</li>
				<li>Rate of messages published</li>
				<li>Rate of messages subscribed</li>
				<li>Success rate</li>
				<li>Average latency time</li>
				<li>90th percentile latency time</li>
			</ul>
		</td>
	</tr>
</table>

### Point-to-point

In a point-to-point scenario, the equal number of clients act as publishers and subscribers respectively.

![point-to-point](_assets/p2p.png)

A use case for this scenario is presented as:
<table>
	<tr>
		<td>Use case</td>
		<td>p2p-25K-250K</td>
	</tr>
	<tr>
		<td>Description</td>
		<td>25,000 publishers publish messages to 25,000 topics which are subscribed by 25,000 subscribers</td>
	</tr>
	<tr>
		<td>Details</td>
		<td>
			<ol>
				<li>50,000 clients are divided into 25,000 groups. Each group has its own distinct topic, 1 publisher and 1 subscriber.</li>
				<li>All publishers and subscribers connect to the TCP port of the broker, using MQTT 3.1.1 protocol. The Keep Alive property is set as 300, and Clean Session is set as 1.</li>
				<li>Once the connection of a subscriber is established, the subscriber immediately subscribes to the topic of that group using QoS 1.</li>
				<li>When all the connections are established, publishers of each group publish message to the topic of that group using QoS 1 with Retain as 0. The publish rate for each publisher is 10 message per second. The payload size of each message is 1 KB.</li>
				<li>Keep the publish and subscribe for 30 minutes. The expected total publish rate is 250,000 messages per second, and the expected total subscribe rate is 250,000 messages per second.</li>
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
				<li>Packets receiving and sending rate</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td>Broker capability metrics of interest</td>
		<td>
			<ul>
				<li>Number of publishers</li>
				<li>Number of subscribers</li>
				<li>Number of topics</li>
				<li>Size of messages</li>
				<li>Rate of messages published</li>
				<li>Rate of messages subscribed</li>
				<li>Success rate</li>
				<li>Average latency time</li>
				<li>90th percentile latency time</li>
			</ul>
		</td>
	</tr>
</table>

### Fan-in

A fan-in scenario is the opposite of fan-out. In a fan-in scenario, a large number of clients act as publishers, with only a few or a single subscriber.

![fan-in](_assets/fanin.png)

A use case for this scenario is presented as:
<table>
	<tr>
		<td>Use case</td>
		<td>sharesub-5000-20K</td>
	</tr>
	<tr>
		<td>Description</td>
		<td>20,000 publishers publish messages to 2,000 topics which are subscribed by 10,000 subscribers</td>
	</tr>
	<tr>
		<td>Details</td>
		<td>
			<ol>
				<li>30,000 clients are divided into 2,000 groups. Each group has its own distinct topic, 10 publishers and 5 subscribers.</li>
				<li>All publishers and subscribers connect to the TCP port of the broker, using MQTT 3.1.1 protocol. The Keep Alive property is set as 300, and Clean Session is set as 1.</li>
				<li>Once the connection of a subscriber is established, the subscriber immediately subscribes to the topic of that group using QoS 1.</li>
				<li>When all the connections are established, publishers of each group publish message to the topic of that group using QoS 0 with Retain as 0. The publish rate for each publisher is 1 message per second. The payload size of each message is 1 KB.</li>
				<li>Keep the publish and subscribe for 30 minutes. The expected total publish rate is 20,000 messages per second, and the expected total subscribe rate is 20,000 messages per second.</li>
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
				<li>Packets receiving and sending rate</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td>Broker capability metrics of interest</td>
		<td>
			<ul>
				<li>Number of publishers</li>
				<li>Number of subscribers</li>
				<li>Number of topics</li>
				<li>Size of messages</li>
				<li>Rate of messages published</li>
				<li>Rate of messages subscribed</li>
				<li>Success rate</li>
				<li>Average latency time</li>
				<li>90th percentile latency time</li>
			</ul>
		</td>
	</tr>
</table>

### Composite

A composite scenario combines scenarios involving connection/publish/subscribe. Usually, a large number of clients connect to the broker, with most of them playing roles of background connections, and the rest of them perform fan-out, point-to-point, or fan-in scenario.

We welcome contributions from the community to enrich use cases!

## Benchmarking results of EMQX

To demonstrate practical examples of benchmarking result, we use EMQX as the MQTT broker and XMeter as the benchmark tool. To perform benchmarks, XMeter simulates a huge number of MQTT clients, executes 3 use cases for connection, fan-out and point-to-point scenarios, and generates a report convering the primary performance metrics.

### EMQX broker configuration

| **Deployment**             | **Version**             | **OS**           | **CPU** | **Memory** | **Cloud host model**        |
| -------------------------- | ----------------------- | ---------------- | ------- | ---------- | --------------------------- |
| 3-nodes-cluster with an LB | EMQX Open source 5.0.11 | CentOS 7.9 64bit | 32vCPUs | 64 GiB     | c6s.8xlarge.2 (Huaweicloud) |

In the benchmark, XMeter connects to the LB that is configured with EMQX via VPC peering.

### Connection case

Refer to “simultaneous-conn-ssl-auth-1M-100K” use case above for details.

#### Result highlight

|                 | **Min. connection time (ms)** | **Max. connection time  (ms)** | **Average connection time  (ms)** | **90th percentile connection time (ms)** | **Connection rate** | **EMQX node CPU usage** | **EMQX node memory usage (GiB)** |
| --------------- | ----------------------------- | ------------------------------ | --------------------------------- | ---------------------------------------- | ------------------- | ----------------------- | -------------------------------- |
| MQTT connection | 10                            | 2601                           | 16.1                              | 16                                       | 2,469.14 per second | max. 18%                | max. 51                          |

#### Result charts

![](_assets/connect-result-charts.png)

### Fan-out case

This composite use case combines connection and fan-out. 1,000,000 clients connect to the EMQX broker, with 1,050 of them performing fan-out scenario (refer to “fanout-5-500K” use case above) and the rest of them as background connections.

#### Result highlight

|                  | **Min. latency time (ms)** | **Max. latency time  (ms)** | **Average latency time  (ms)** | **90th percentile latency time (ms)** | **Message throughput** | **EMQX node CPU usage** | **EMQX node memory usage (GiB)** |
| ---------------- | -------------------------- | --------------------------- | ------------------------------ | ------------------------------------- | ---------------------- | ----------------------- | -------------------------------- |
| MQTT  connection | 2                          | 2012                        | 106                            | 6                                     | 2500.00 per second     | 57% - 64%               | 9.1                              |
| MQTT publish     | 1                          | 841                         | 1.9                            | 3                                     | 2,465.58 per second    |                         |                                  |
| MQTT subscribe   | 0                          | 1401                        | 2.8                            | 2                                     | 493,043.89 per second  |                         |                                  |

#### Result charts

![](_assets/fanout-result-charts.png)

### Point-to-point case

This composite use case combines connection and point-to-point. 1,000,000 clients connect to the EMQX broker, with 50,000 of them performing point-to-point scenario (refer to “p2p-25K-250K” use case above) and the rest of them as background connections.

#### Result highlight

|                 | **Min. latency time (ms)** | **Max. latency time  (ms)** | **Average latency time  (ms)** | **90th percentile latency time (ms)** | **Message throughput** | **EMQX CPU usage** | **EMQX memory usage (GiB)** |
| --------------- | -------------------------- | --------------------------- | ------------------------------ | ------------------------------------- | ---------------------- | ------------------ | --------------------------- |
| MQTT connection | 2                          | 1416                        | 10.9                           | 6                                     | 2500.00 per second     | 65% - 70%          | 9.7                         |
| MQTT publish    | 0                          | 1422                        | 5.5                            | 8                                     | 246,518.27per second   |                    |                             |
| MQTT subscribe  | 0                          | 655                         | 6.7                            | 12                                    | 246,508.62 per second  |                    |                             |

#### Result charts

![](_assets/p2p-result-charts.png)
