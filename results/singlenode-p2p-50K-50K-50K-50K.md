# Benchmark results of UC: singlenode-p2p-50K-50K-50K-50K

## Brokers

| Opensource **Broker** | **Version** |
| --------------------- | ----------- |
| EMQX 4                | 4.4.16      |
| EMQX 5                | 5.0.21      |
| Mosquitto             | 2.0.15      |
| NanoMQ                | 0.17.0      |

## Benchmark use case: singlenode-p2p-50K-50K-50K-50K

In this use case, 50,000 publishers publish messages to 50,000 topics, and 50,000 subscribers subscribe messages. Specially, the use case is executed in the following way:

1. All publishers and subscribers connect to the TCP port of the broker.
2. Once the connection of a subscriber is established, the subscriber immediately subscribes to a topic using QoS 1. Different subscribers subscribe to different topics.
3. When all the connections are established, each publisher publishes message to a topic using QoS 1 with Retain as 0. Different publishers subscribe to different topics. The publish rate for each publisher is 1 message per second. The payload size of each message is 16 bytes.
4. Keep the publish and subscribe for 30 minutes. The expected total publish rate is 50,000 messages per second, and the expected total subscribe rate is 50,000 messages per second.



MQTT protocol 3.1.1 is used for the use case, and other configured MQTT properties are:

<table>
	<tr>
		<td>TLS authentication enablement</td>
		<td>No</td>
	</tr>
	<tr>
		<td>Authentication enablement</td>
		<td>No</td>
	</tr>
	<tr>
		<td>Keep Alive (seconds)</td>
		<td>300</td>
	</tr>
	<tr>
		<td>Clean Session</td>
		<td>1</td>
	</tr>
</table>

## Testbed

The use case is executed on single node. XMeter (version 3.2.4) is used as the benchmark tool.

**HW Details**

- public cloud: AWS
- instance type: c5.4xlarge 16C32G
- OS: Ubuntu 22.04.1 amd64

## Metrics

|               | Actual msg rate | Average pub-to-sub latency (ms) | Max CPU user+system | Avg CPU user+system | Max memory used | Avg memory used |
| ------------- | --------------- | ------------------------------- | ------------------- | ------------------- | --------------- | --------------- |
| **EMQX 4**    | 50k:50k         | 1.68                            | 88%                 | 80%                 | 6.19G           | 5.22G           |
| **EMQX 5**    | 50k:50k         | 2.17                            | 89%                 | 82%                 | 6.7G            | 5.9G            |
| **Mosquitto** | 37k:37k         | 335.94                          | 7%                  | 6%                  | 354M            | 328M            |
| **NanoMQ**    | 50k:50k         | 64.48                           | 34%                 | 31%                 | 1.3G            | 1.3G            |

> in this scenario, Mosquitto couldn't reach to the designed message rate. It stabilized at 37300/s for both pub and sub. 
>
> EMQX 4, EMQX 5 and NanoMQ kept the stable pub & sub rate at 50000/s during the test.

###  Appendix: result charts

#### EMQX 4

![EMQX 4](../_assets/emqx-4.4.16-p2p-result-charts.png)

#### EMQX 5

![EMQX 5](../_assets/emqx-5.0.21-p2p-result-charts.png)

#### Mosquitto

![Mosquitto](../_assets/mosquitto-2.0.15-p2p-result-charts.png)

#### NanoMQ

![NanoMQ](../_assets/nano-0.17.0-p2p-result-charts.png)

