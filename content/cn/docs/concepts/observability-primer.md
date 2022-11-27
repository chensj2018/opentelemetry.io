---
title: Observability基础
description: >-
  在深入研究Opentelemetry之前，介绍一些核心概念
weight: 9
description: >-
 在深入研究into OpenTelemetry之前，首先理解一些核心概念是很重要的。
---
## 什么是Observability?

Observability让我们从外部视角理解一个系统，也就是说在不了解系统内部工作原理的情况下，通过提问题的方式了解这个系统。此外，还能让我们轻松地解决和处理新的问题（即未知的未知的问题），以及帮助我们回答：“系统为什么会发生这种问题？”

为了能够回答系统的这些问题，应用程序必须适当地instrument。也就是说，应用程序必须emit
[signals](/docs/concepts/signals/) 例如
[traces](/docs/concepts/observability-primer/#distributed-traces),
[metrics](/docs/concepts/observability-primer/#reliability--metrics), 和
[logs](/docs/concepts/observability-primer/#logs)。一个应用程序适当地instrument，是指开发者已经获取到了解决系统问题所需的足够信息。

[**OpenTelemetry**](/docs/concepts/what-is-opentelemetry) 是一种instrument·应用程序代码的机制，用于帮助一个系统具有可观测性。

## Reliability & Metrics

**Telemetry** 是指来自一个系统描述其行为的数据。这些数据的形式可以是[Traces](#distributed-traces),
[Metrics](#reliability--metrics), 和 [Logs](#logs)。

**Reliability** 回答的问题是：“一个服务是否正在做用户期望它做的事？”一个系统可能100%的时间都是在线的，但是如果一个用户点击“添加到购物车”想将一条黑色的裤子添加他的购物车，实际上却是添加了一条红色的裤子，如果出现这种情况，这个系统被称为是不可靠的。

**Metrics** 是你的基础设施或应用程序一段时间内聚合的数字数据。例如，包括|系统错误率、CPU利用率、和某一个service的请求率等。

**SLI** 或Service Level Indicator，是service的测量指标。一个好的SLI是从用户的视角测量你的service。网页加载速度就是一个SLI的例子。

**SLO**或Service Level Objective,是指向其他对象或组织传递其可靠性的对象。这需要一个或多个有商业价值的SLI实现。

## 理解分布式Tracing

为了理解分布是Tracing，让我们从一些基本概念开始。

### Logs

**Log**是service或其他组件发出的带有时间戳的消息。不像[Traces](#distributed-traces)，不需要和特定的用户请求或事务绑定到一起。软件的任意地方都有可能产生Log，过去开发者和运维人员严重依赖Log，以理解系统的行为。

Log例子:

```text
I, [2021-02-23T13:26:23.505892 #22473]  INFO -- : [6459ffe1-ea53-4044-aaa3-bf902868f730] Started GET "/" for ::1 at 2021-02-23 13:26:23 -0800
```

很不幸，log在跟踪代码执行方面不是很有用，通常缺少上下文信息，例如不知道函数是哪里调用的。

当增加[Span](#spans)信息，log变的更加有用。

### Spans

一个 **Span** 表示一个工作或操作的单元。它跟踪一个具体的操作，例如在执行期间产生了一个请求，画了一幅图。

Span包含：名字、时间有关的数据、 [结构化的日志信息](/docs/concepts/signals/traces/#span-events), and

[其他元数据 (i.e. Attributes)](/docs/concepts/signals/traces/#attributes)，用于提供它跟踪的操作的信息。

下面是一个Span中信息的示例：

#### Span Attributes

| Key              | Value                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------- |
| net.transport    | IP.TCP                                                                                                                |
| net.peer.ip      | 10.244.0.1                                                                                                            |
| net.peer.port    | 10243                                                                                                                 |
| net.host.name    | localhost                                                                                                             |
| http.method      | GET                                                                                                                   |
| http.target      | /cart                                                                                                                 |
| http.server_name | frontend                                                                                                              |
| http.route       | /cart                                                                                                                 |
| http.scheme      | http                                                                                                                  |
| http.host        | localhost                                                                                                             |
| http.flavor      | 1.1                                                                                                                   |
| http.status_code | 200                                                                                                                   |
| http.user_agent  | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36 |

> Span的更多信息和如何适应OTel，参见[Spans in OpenTelemetry](/docs/concepts/signals/traces/#spans-in-opentelemetry).

### 分布式Traces

在多服务架构中（例如微服务和serverless应用），一个**Distributed Trace**（更常见的称为**Trace**中通过记录所有请求的path（由应用程序或终端用户产生），记录请求的传播信息。

如果没有tracing，在分布式系统中定位性能问题是一个极具挑战的任务。

它提高了应用程序或系统健康状况的可见性，让我们解决很难在本地复现的bug。Tracing对分布式系统是至关重要的，通常用于解决不确定或在本地环境无法复现的问题。

通过将贯穿分布式系统的请求拆分成多个Span，Tracing降低了调试和理解分布式系统的难度。

一个Trace由一个或多个Span组成，第一个Span称为Root Span。每个Root Span表示从开始到结束的一个请求。其他Span位于它们的父Span之下，提供更深一层的上下文，描述请求发生了什么或哪步产生了新的请求。

有很多Observability back-end使用瀑布图可视化trace，它们看起来像是这样：

![Trace示例](/img/waterfall_trace.png "trace waterfall diagram")

瀑布图展示了root span和子span之间的父子关系。当一个span覆盖了另一个span，这也代表了他们是嵌套关系。

> 有关trace的更多信息和如何适配OTel，请参考[Traces in OpenTelemetry](/docs/concepts/signals/traces/).
