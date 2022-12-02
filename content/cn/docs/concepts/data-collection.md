---
title: "Data Collection"
description: >-
  OpenTelemetry项目通过Collector使收集远程监测数据变得容易
weight: 50
---
OpenTelemetry项目通过Collector使收集远程监测数据变得容易。OpenTelemetry Collector提供了与厂商无关的实现，包括如何接收、处理、导出远程监测数据。使得不用运行、操作和维护多个agent/collector，以便支持多种数据格式（例如Jaeger，Prometheus等）发送到一个或多个开源或商业的back-end。另外，Collector为终端用户提供控制数据的途径。监测库默认将远程监测数据导出到Collector。

> Collector也许是一个分布式系统，更多信息请查看[这里](../distributions)。

## 部署

OpenTelemetry Collector提供了单一的二进制文件和两种部署方式：

- **Agent:** 一个Collector实例，与应用程序同时运行或在相同主机上（例如，二进制，sidecar或daemonset）。
- **Gateway:** 作为独立服务的一个或多个Collector实例(例如，容器或deployment) ，通常每个集群，数据中心或区域部署一个。

有关如何使用Collector的更多信息，请参阅[getting started documentation](/docs/collector/getting-started).

## 组件

Collector由下面这些组件组成：

- <img width="32" src="https://raw.github.com/open-telemetry/opentelemetry.io/main/iconography/32x32/Receivers.svg"></img>
  `receivers`: 实现Collector如何收集数据;支持push和pull
- <img width="32" src="https://raw.github.com/open-telemetry/opentelemetry.io/main/iconography/32x32/Processors.svg"></img>
  `processors`: 实现怎么处理接收到的数据
- <img width="32" src="https://raw.github.com/open-telemetry/opentelemetry.io/main/iconography/32x32/Exporters.svg"></img>
  `exporters`: 实现将收到的数据发送到哪里；支持push和pull

通过 ` pipelines`使能这些组件，使用yaml配置将多个组件实例配置成一个 ` pipeline`  。

有关组件的更多信息，请参阅[configuration documentation](/docs/collector/configuration).

## 制品库

OpenTelemetry项目提供了两个版本的Collector：

- **[Core](https://github.com/open-telemetry/opentelemetry-collector/releases):**
  基础组件，例如配置，常用的receivers, processors, exporters, and extensions.
- **[Contrib](https://github.com/open-telemetry/opentelemetry-collector-contrib/releases):**
  所有核心组件，加上可选的或可能实验性的组件。为流行的开源项目（包括Jaeger，Prometheus和Fluent Bit）提供支持。也包括更多专用的或商业的receivers,processors, exporters, and extensions。
