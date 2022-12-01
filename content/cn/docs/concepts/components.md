---
title: Components
description: >-
  构成OpenTelemetry的主要组件
weight: 20
---
OpenTelemetry当前有如下几个主要组件：

- 跨开发语言的规范
- 收集、转换和导出遥测数据的工具集
- 各种开发语言的SDKs
- 自动指标采集和代码包

OpenTelemetry使你摆脱了对特定供应商的SDK、生成和导出遥测工具的依赖。

## Specification

描述了跨开发语言的需求和对所有实现的期望。规范可以分为以下几类：

- **API:** 定义数据类型和操作，这些操作用于产生和关联tracing、metrics和log数据
- **SDK:** 定义使用特定开发语言实现API的需求。这里也定义了配置、数据处理、导出等概念
- **Data:** 定义OpenTelemetry Protocol (OTLP)和与厂商无关的、back-end支持的语义公约

更多信息请参阅 [Specification](/docs/reference/specification/).

此外，广泛使用的protobuf格式的接口文件，可以在[proto repository](https://github.com/open-telemetry/opentelemetry-proto)找到。

## Collector

OpenTelemetry Collector是与厂商无关的代理服务，用于接收、处理和导出遥测数据。支持接收多种格式的遥测数据（例如，OTLP、Jaeger、Prometheus以及许多商用/专用的工具），发送数据到一个或多个back-end。也支持在导出之前，处理和过滤遥测数据。Collector发行包支持更多的数据格式和商用back-end。

更多信息，请参阅 [Data Collection](/docs/concepts/data-collection/).

## Language SDKs

OpenTelemetry还有各种开发语言的SDK，用于产生遥测数据和导出数据到back-end。这些SDK可以将使用通用库的自动instrument和自己应用程序的手动instrument整合到一起。

更多信息，请参阅[Instrumenting](/docs/concepts/instrumenting).

## Automatic Instrumentation

OpenTelemetry有非常多的组件，从常用的库和框架产生遥测数据。例如，使用HTTP库将自动产生这些请求的遥测数据。不同的开发语言使用自动instrument有很大区别，一些需要和你的应用程序一起加载，而另外一些也许需要显式的引入到你的代码基线。

不使用独立的组件实现常用库的可观测性，将是一个长期的目标。

更多信息前参阅[Instrumenting Libraries](/docs/concepts/instrumenting-library/)。
