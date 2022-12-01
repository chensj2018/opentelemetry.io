---
title: "Instrumenting"
description: >-
  OpenTelemetry如何便于自动和手动的采集应用程序的遥测数据
weight: 40
---
为了使一个系统可观测，它必须是可度量的。也就是说，代码必须产生[traces](/docs/concepts/observability-primer/#distributed-traces),
[metrics](/docs/concepts/observability-primer/#reliability--metrics), 和
[logs](/docs/concepts/observability-primer/#logs)。OpenTelemetry通过自动和手动的方式，方便度量你的应用程序。

确实不同的开发语言，安装OpenTelemetry的方式有所不同，下面章节涵盖了他们相同之处。

## Automatic Instrumentation

### 添加依赖

为了是能自动度量，需要添加一个或多个依赖。如何添加特定开发语言的依赖？至少，这些依赖有添加到OpenTelemetry API和SDK的能力。一些语言还需要为每种度量添加依赖关系。Exporter依赖也是需要的。有关OpenTelemetry API和SDK的更多信息，请参阅[specification](/docs/reference/specification/)。

### 配置OpenTelemetry度量

通过环境变量和系统属性等（与具体开发语言相关）是能这些配置。至少service名称必须是可配置的，用于区分被度量的service。其他可选的可能包括：

- 数据源的特定配置
- Exporter配置
- Propagator配置
- Resource配置

## Manual Instrumentation

### 导入OpenTelemetry API和SDK

首先必须将OpenTelemetry导入到你的service代码中。如果开发的是一个库或者其他不能独立运行的组件，只需要依赖API，否则需要API和SDK。有关更多OpenTelemetry API和SDK的信息，请参阅[specification](/docs/reference/specification/)。

### 配置OpenTelemetry API

为了创建traces或者metrics，首先需要创建一个tracer或meter provider。通常，推荐的用法是SDK提供单例的默认provider。然后通过这个provider可以得到一个tracer或meter实例，当然这个过程中需要你提供名字和版本。选择的名字需要能准确地标识被度量的对象，例如你正在开发一个库，应该以你的库命名它（例如 `com.legitimatebusiness.myLibrary`）。同时推荐提供一个字符串的版本号（例如 `semver:1.0.0`）标识库或service的当前版本。

### 配置OpenTelemetry SDK

如果正在构建一个service，需要使用恰当的方式配置SDK，使得遥测数据导出到用于分析的back-end。推荐使用可被编辑的配置文件或其他机制处理配置项。对于不同开发语言，优选的策略有所不同。

### 创建遥测数据

一旦配置完API和SDK，便可以通过从provider获取的tracer和meter对象，自由地创建trace和metric事件。也可以利用插件或在代码中内嵌的方式创建trace和meter事件。

### 导出数据

一旦已经创建了遥测数据，肯定希望将其发送到其他地方。OpenTelemetry支持两种主要方式导出遥测数据到back-end：直接从进程导出或通过代理的方式（[OpenTelemetry Collector](/docs/collector)）。

进程内export需要引入和依赖一个或多个*exporter*，这些库将实现将OpenTelemetry的内存span和metric对象转换成遥测分析工具（如Jaeger或Prometheus）需要的恰当数据格式。另外，OpenTelemetry支持一种称为 `OTLP`的通信协议，它被所有OpenTelemetry SDK支持。这种协议被用于发送数据到OpenTelemetry Collector。Collector是一个独立的进程，作为service实例的代理或sidecar，或者运行在单独的主机上。这个Collector能够被配置为转发和导出数据到选择的分析工具上。

除了开源的如Jaeger或Promethus工具，越来越多的公司支持从OpenTelemetry采集遥测数据。更多细节请参阅[this page](/vendors) 。
