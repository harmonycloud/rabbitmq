# RabbitMQ Message Broker Service

**English** | [中文](README_zh.md)

Enterprise-grade RabbitMQ message broker service for Kubernetes with high availability, flexible routing, and integrated management console.

## Overview

RabbitMQ is an open-source distributed message broker that implements the Advanced Message Queuing Protocol (AMQP). This package delivers a production-ready RabbitMQ cluster on Kubernetes, featuring reliable message delivery, flexible routing, mirrored queues, and integrated monitoring. RabbitMQ supports multiple messaging patterns and is well suited for microservice architectures, event-driven architectures, and asynchronous communication in distributed systems.

## Features

### Core Capabilities
- **Reliable messaging**: Durable message delivery with publish and consume acknowledgments
- **Multiple protocols**: AMQP, MQTT, STOMP protocol support
- **Flexible routing**: Multiple exchange types (Direct, Fanout, Topic, Headers)
- **Queue management**: Persistent queues, delayed queues, and dead-letter queues
- **Message acknowledgment**: Publisher confirms and consumer acknowledgments
- **Flow control**: Producer and consumer flow control mechanisms

### Advanced Features
- **Cluster management**: Multi-node cluster deployment
- **High availability**: Mirrored queues with automatic failover
- **Management console**: Integrated web management interface
- **Access control**: User, virtual host, and permission management
- **Monitoring and alerting**: Integrated Prometheus metrics and alert rules
- **Log management**: Structured logging output with log collection

### Enterprise Features
- **Message persistence**: Message and queue persistence
- **Transaction support**: Message transactions
- **Priority queues**: Message priority queue support
- **TTL settings**: Message and queue TTL configuration
- **Dead-letter queues**: Dead-letter message processing
- **Delayed queues**: Delayed message delivery

### Operations Features
- **Resource management**: CPU and memory resource limits
- **Node affinity**: Pod anti-affinity and node affinity configuration
- **Tolerations**: Taint toleration settings
- **Health checks**: Built-in liveness and readiness probes
- **Metrics export**: Prometheus-format metrics
- **Graceful shutdown**: Graceful shutdown mechanism

## Supported Versions

### RabbitMQ Releases
- **3.12.7** (latest stable, includes management plugin)

### Component Releases
- **Package version**: 1.2.0-1.2.0
- **RabbitMQ Init**: v1.0.0

## Architecture

### Deployment Modes

#### 1. Operator Standard (operator-standard)
- **Use cases**: Development, testing, and quick deployment
- **Traits**: Single replica, minimal resource usage
- **Topology**: 1 RabbitMQ instance

#### 2. Operator Highly Available (operator-highly-available)
- **Use cases**: Production environments
- **Traits**: Multi-replica with automatic failover
- **Topology**: 3 RabbitMQ instances

#### 3. Cluster (cluster)
- **Use cases**: Production environments with large-scale message processing
- **Traits**: High availability, high performance, horizontally scalable
- **Topology**: 3+ instances (configurable)

#### 4. Cluster Active-Active (cluster-active)
- **Use cases**: Multi-site active-active deployments
- **Traits**: Cross-datacenter cluster replication

### Technical Architecture

```
+---------------------------------------------------------+
|                   RabbitMQ Cluster                       |
+---------------------------------------------------------+
|  +-----------+  +-----------+  +-----------+            |
|  | RabbitMQ  |  | RabbitMQ  |  | RabbitMQ  |            |
|  |  Node 1   |  |  Node 2   |  |  Node 3   |            |
|  |           |  |           |  |           |            |
|  | +-------+ |  | +-------+ |  | +-------+ |            |
|  | |Queue 1| |  | |Queue 2| |  | |Queue 3| |            |
|  | |Mirror | |  | |Mirror | |  | |Mirror | |            |
|  | +-------+ |  | +-------+ |  | +-------+ |            |
|  +-----------+  +-----------+  +-----------+            |
+---------------------------------------------------------+
|                  Message Routing Layer                   |
|  +-----------+  +-----------+  +-----------+            |
|  |  Direct   |  |  Fanout   |  |  Topic    |            |
|  | Exchange  |  | Exchange  |  | Exchange  |            |
|  +-----------+  +-----------+  +-----------+            |
+---------------------------------------------------------+
|                  RabbitMQ Operator                       |
|  +-----------+  +-----------+  +-----------+            |
|  |  Manager  |  |Controller |  |  Webhook  |            |
|  +-----------+  +-----------+  +-----------+            |
+---------------------------------------------------------+
|               Kubernetes Resources                      |
|  StatefulSet | Service | PVC | ConfigMap | Secret | Job |
+---------------------------------------------------------+
```

### Component Overview

- **RabbitMQ**: Core message broker engine with management plugin
- **RabbitMQ Operator**: Kubernetes operator for lifecycle management
- **RabbitMQ Init**: Initialization container for cluster bootstrap

## Prerequisites

- Kubernetes 1.26+
- [OpenSaola Operator](https://github.com/harmonycloud/opensaola) deployed
- [saola-cli](https://github.com/harmonycloud/saola-cli) installed

## Quick Start

```bash
# Publish the package
saola publish rabbitmq/

# Install the operator
saola operator create rmq-operator --type RabbitMQ --version 3.12.7

# Create an instance
saola middleware create my-rabbitmq --type RabbitMQ --version 3.12.7

# Check status
saola middleware get my-rabbitmq
```

## Available Actions

| Action | Description |
|--------|-------------|
| restart | Restart the middleware instance |

## Configuration

Key parameters can be customized via the baseline configuration. See `manifests/*parameters.yaml` for the full parameter reference:

- `manifests/clusterparameters.yaml` -- Cluster mode parameters
- `manifests/clusteractiveparameters.yaml` -- Active-active cluster parameters

## Usage Guidance

### Environment Selection

#### Development and Test
- **Recommended topology**: Operator Standard
- **Resources**: CPU 2 cores, memory 4 Gi, storage 50 Gi
- **Suggested version**: RabbitMQ 3.12.7
- **Instances**: 1

#### Production
- **Recommended topology**: Cluster or Operator Highly Available
- **Resources**: CPU 4 cores, memory 8 Gi, storage 100 Gi
- **Suggested version**: RabbitMQ 3.12.7
- **Instances**: 3+ for high availability

### Best Practices

#### Security
- Configure user authentication and virtual host isolation
- Enforce strong passwords with mixed character classes
- Set fine-grained permissions per virtual host
- Rotate credentials periodically
- Enable TLS encryption for all connections

#### Performance Tuning
- Set appropriate queue length limits
- Configure message TTL for expiration
- Enable message persistence for important queues
- Tune prefetch counts for consumers
- Choose the right exchange type for your routing pattern

#### Queue Management
- Configure mirrored queues for critical workloads
- Distribute queues across nodes for load balancing
- Use dead-letter queues for failed message handling
- Monitor cluster load balance metrics
- Clean up expired data regularly

#### Monitoring and Alerting
- Track node status, queue depth, and connection count
- Monitor message throughput (publish and consume rates)
- Define alert thresholds for message backlog
- Watch disk usage and memory consumption
- Review channel and connection metrics

#### Producer Best Practices
- Use connection pools for efficient resource usage
- Implement retry mechanisms for publish failures
- Handle publish confirmations properly
- Monitor publish performance and latency

#### Consumer Best Practices
- Implement idempotent consumption
- Set appropriate concurrency levels
- Handle consumption failures gracefully
- Monitor consumption progress and lag

## Related Projects

| Project | Description |
|---------|-------------|
| [OpenSaola Operator](https://github.com/harmonycloud/opensaola) | Core Kubernetes operator for middleware lifecycle management |
| [saola-cli](https://github.com/harmonycloud/saola-cli) | Command-line tool for middleware management |
| [PostgreSQL](https://github.com/harmonycloud/postgresql) | PostgreSQL database package |
| [MySQL](https://github.com/harmonycloud/mysql) | MySQL database package |
| [Kafka](https://github.com/harmonycloud/kafka) | Apache Kafka streaming platform package |
| [Redis](https://github.com/harmonycloud/redis) | Redis in-memory data store package |
| [Elasticsearch](https://github.com/harmonycloud/elasticsearch) | Elasticsearch search engine package |
| [ZooKeeper](https://github.com/harmonycloud/zookeeper) | Apache ZooKeeper coordination service package |

## License

This project is licensed under the [Apache License 2.0](LICENSE).
