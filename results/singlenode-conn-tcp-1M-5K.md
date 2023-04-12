# Benchmark results of singlenode-conn-tcp-1M-5K

## Brokers

| Opensource **Broker** | **Version** |
| --------------------- | ----------- |
| EMQX 4                | 4.4.16      |
| EMQX 5                | 5.0.21      |
| Mosquitto             | 2.0.15      |
| NanoMQ                | 0.17.0      |

## Benchmark use case: singlenode-conn-tcp-1M-5K

In this testcase, 1,000,000 clients connect to the broker within 200 seconds (e.g. cps 5K) and all clients are kept online for 30 minutes. 

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

|               | Average  latency (ms) | Max CPU user+system | Avg CPU user+system | Max memory used | Avg memory used |
| ------------- | --------------------- | ------------------- | ------------------- | --------------- | --------------- |
| **EMQX 4**    | 2.21                  | 44%                 | 31%                 | 10.87G          | 8.68G           |
| **EMQX 5**    | 2.25                  | 52%                 | 24%                 | 16.1G           | 12.1G           |
| **Mosquitto** | 5.61                  | 2%                  | 2%                  | 1G              | 1G              |
| **NanoMQ**    | 2.99                  | 5%                  | 4%                  | 10.6G           | 10.6G           |

###  Appendix: result charts

#### EMQX 4



#### EMQX 5



#### Mosquitto



#### NanoMQ