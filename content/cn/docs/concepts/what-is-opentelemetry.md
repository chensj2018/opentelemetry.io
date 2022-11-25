---
title: "OpenTelemetry是什么?"
description: >-
  OpenTelemetry背景信息
weight: 10
---
微服务架构使得开发者能够更快和更独立的构建和发布软件，这得益于他们摆脱了单体架构冗长的发布流程。

当这些分布式系统变大以后，对开发者来说观测这些服务之间的依赖或影响关系变得越来越困难，特别是部署之后或停机期间，可测性的速度和精确性是至关重要的。

> [可观测性](/docs/concepts/observability-primer/#what-is-observability)
>
> 使得开发者和运维人员能获取他们系统的内部状态。

## So what?

为了使系统可观测，它必须是可测量的。也就是说，代码必须能够产生 [traces](/docs/concepts/observability-primer/#distributed-traces),
[metrics](/docs/concepts/observability-primer/#reliability--metrics), 和
[logs](/docs/concepts/observability-primer/#logs)，这些测量数据必须被发送到可观测性的back-end。现在已经有一些可观测性的back-end，范围从自己托管的开源工具(例如
[Jaeger](https://www.jaegertracing.io/) 和 [Zipkin](https://zipkin.io/)), 到商业SaaS产品。

在过去，测量代码的方式是多种多样的，因此每种可观测back-end都有自己的instrumentation库和推送数据的agent。

这意味着没有标准化的数据格式。此外，如果一家公司决定替换可观测性back-end，意味着他们不得不重写测量指标代码和配置新的agent，以便能够将遥测数据发送到新的back-end。

> 缺乏标准的直接后果是，数据缺乏可移植性以及用户维护instrumentation库的负担。

认识到标准化的需求，云原生社区开发了两个开源项目：[OpenTracing](https://opentracing.io) (a
[Cloud Native Computing Foundation (CNCF)](https://www.cncf.io) project) 和
[OpenCensus](https://opencensus.io) (a
[Google Open Source](https://opensource.google) community project)。

**OpenTracing** 提供了厂商中立的API，用于发送遥测数据到可观测性back-end。然而依赖于开发者自己实现满足标准的库。

**OpenCensus** 提供了一套特定语言的库，用于instrument他们的代码，并发送到任何一个它支持的back-end。

## 你好，OpenTelemetry!

一个单一的标准更有价值，于是[在2019年五月][cncf-incubating-project]OpenCensus和OpenTracing合并为 OpenTelemetry (简称OTel) 。作为CNCF的一个孵化项目，OpenTelemetry结合了两者的优点，以及增加了一下新特性。

OTel的目标是提供一套与厂商无关、标椎化的SDK、API和
[tools](/docs/collector)，用于采集、转换和发送数据到可观测性back-end(包括开源或商业的供应商)。

## OpenTelemetry能为我做什么?

OTel在行业内已被广泛的支持和采用，包括云服务提供者、
[供应商](/vendors)和终端用户。它提供了：

- 一个单一的、厂商无关的instrumentation库，对于
  [每种语言](/docs/instrumentation)都可以自动或手动instrumentation。
- 一个单一的、厂商中立的[collector](/docs/collector) binary，并且可以使用多种方式部署。
- 一个端到端的遥测数据实现，包括：产生、emit、collect、处理、导出；
- 数据全流程的控制能力，通过配置将数据并行发送到多个destinations；
- 开放的标准，确保供应商无关的数据collection。
- 同时支持多种
  [context propagation](/docs/reference/specification/overview/#context-propagation)格式，并协助其演进为标准协议；
- 无论你处于observablity哪个阶段，OTel都为你提供一条前进的道路。

因为有各种各样的[开源和商业][otel-collector-contrib]支持, 很容易从OpenTracing和OpenCensus迁移到OpenTelmetry。

## OpenTelemetry不是什么

OpenTelemetry不是像Jaeger或Prometheus这样的observalility back-end。相反，它支持导出数据到各种各样的开源或商用的back-ends。它提供了一个可插拔的技术架构，因此很容易加入额外的技术协议和数据格式。

[cncf-incubating-project]: https://www.cncf.io/blog/2021/08/26/opentelemetry-becomes-a-cncf-incubating-project/

[otel-collector-contrib]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver
