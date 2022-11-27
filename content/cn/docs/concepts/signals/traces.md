---
title: Traces
description: >-
  Traces给了我们一个请求内部发生了什么的全局视图，这些请求有来自用户或应用程序。
weight: 1
---
## Tracing in OpenTelemetry

[**Traces**](/docs/concepts/observability-primer/#distributed-traces) 给了我们一个请求内部发生了什么的全局视图，这些请求有来自用户或应用程序。通过trace微服务和相关应用程序，OpenTelemetry提供了一种将可观测性的引入到产品代码中的实现方式。

Trace示例:

```json
{
    "name": "Hello-Greetings",
    "context": {
        "trace_id": "0x5b8aa5a2d2c872e8321cf37308d69df2",
        "span_id": "0x5fb397be34d26b51",
    },
    "parent_id": "0x051581bf3cb55c13",
    "start_time": "2022-04-29T18:52:58.114304Z",
    "end_time": "2022-04-29T18:52:58.114435Z",
    "attributes": {
        "http.route": "some_route1"
    },
    "events": [
        {
            "name": "hey there!",
            "timestamp": "2022-04-29T18:52:58.114561Z",
            "attributes": {
                "event_attributes": 1
            }
        },
        {
            "name": "bye now!",
            "timestamp": "2022-04-29T22:52:58.114561Z",
            "attributes": {
                "event_attributes": 1
            }
        }
    ],
}
{
    "name": "Hello-Salutations",
    "context": {
        "trace_id": "0x5b8aa5a2d2c872e8321cf37308d69df2",
        "span_id": "0x93564f51e1abe1c2",
    },
    "parent_id": "0x051581bf3cb55c13",
    "start_time": "2022-04-29T18:52:58.114492Z",
    "end_time": "2022-04-29T18:52:58.114631Z",
    "attributes": {
        "http.route": "some_route2"
    },
    "events": [
        {
            "name": "hey there!",
            "timestamp": "2022-04-29T18:52:58.114561Z",
            "attributes": {
                "event_attributes": 1
            }
        }
    ],
}
{
    "name": "Hello",
    "context": {
        "trace_id": "0x5b8aa5a2d2c872e8321cf37308d69df2",
        "span_id": "0x051581bf3cb55c13",
    },
    "parent_id": null,
    "start_time": "2022-04-29T18:52:58.114201Z",
    "end_time": "2022-04-29T18:52:58.114687Z",
    "attributes": {
        "http.route": "some_route3"
    },
    "events": [
        {
            "name": "Guten Tag!",
            "timestamp": "2022-04-29T18:52:58.114561Z",
            "attributes": {
                "event_attributes": 1
            }
        }
    ],
}
```

这个示例中有三个子项，名字分别为： "Hello-Greetings",
"Hello-Salutations" 和 "Hello"。因为每个请求的上下文(context)里有相同的Trace ID，所以所有信息都可以整合到一起。通过请求的路由、时间戳和其他属性，得到一个完整的线索。

为了理解在OpenTelemetry中trace是如何工作的，让我们来看一下组成trace的组件列表:

- Tracer
- Tracer Provider
- Trace Exporter
- Trace Context

### Tracer Provider

Tracer Provider (有时称为 `TracerProvider`)是一个 `Tracer`的工厂。在大多数应用程序中，Tracer Provider只被初始化一次，生命周期和应用程序相同。同时资源和Exporter的初始化也包含在Tracer Provider的初始化之中，通常这是OpenTelemetry 中Trace初始化的第一步。在某些语言SDK中，一个全局的Tracer Provider已经为你初始化好了。

### Tracer

Tracer创建包含更多信息的span，这些信息描述了特定操作发生了什么，例如一个service的请求。Tracer由Tracer Provider创建。在某些语言中，一个全局的Tracer已经为你初始化好了。

### Trace Exporters

Trace Exporters发送trace信息到消费者， 这些消费者是为了调试和开发目的的标准输出，如OpenTelemetry Collector，任何开源或商用的back-end。

### Trace Context

Trace Context是span的元数据，提供跨service和进程边界的span之间的联系信息。例如，A service调用了B service，同时我们想用trace机制跟踪这些调用。在这种情况下，OpenTelemetry将使用Trace Context封装trace和当前A service的span的ID，所以在Sevice B中创建的span能够连接和添加到trace中。

这就是著名的Context Propagation。

### Context Propagation

Context Propagation是使能分布式Tracing的核心概念。通过context Propagation，能够将span彼此关联起来，组成一个trace，无论这些span是哪里产生的。我们通过Context和Propagation两个子概念定义Context Propagation。

**Context** 是一个对象，包含发送和接收service的信息，用于将一个span和另外一个span关联起来，从而形成一个全局trace。

**Propagation** 是一种在service或进程之间传播context机制。通过这种机制，实现分布式trace的组装。它序列化和反序列化span contex，从一个service到另外一个service传播trace相关的信息。我们现在称之为：**Trace Context**。

在OpenTelemetry中，有另外一种形式的Context。例如，某个context是W3C `TraceContext`规范在span上的实现，在OpenTelemetry中称之为 `SpanContext`。

我们识别Span Context使用四个主要部分：`traceID`、`spanID`、**Trace Flags**和 **Trace State**。

**`traceID`** - 一个唯一的16-byte数组，用于标识span所属的trace。

**`spanID`** - 十六进制编码的8-byte数组，用于标识当前span。

**Trace Flags** - 提供更多有关trace的细节信息，例如它的采样。

**Trace State** - 提供更多供应商信息，用于trace多个分布式系统。更多信息请参阅[W3C Trace Context](https://www.w3.org/TR/trace-context/#trace-flags) 。

通过结合Context和Propagation，你现在可以组装一个trace了。

> 更多信息, 请参阅 [traces 规范][traces specification]

## Spans in OpenTelemetry

一个[**Span**](/docs/concepts/observability-primer/#spans) 代表一个工作或操作单元，是构建trace的模块。在OpenTelemetry中，包括如下信息：

- Name
- Parent span ID (empty for root spans)
- Start and End Timestamps
- [Span Context](#span-context)
- [Attributes](#attributes)
- [Span Events](#span-events)
- [Span Links](#span-links)
- [Span Status](#span-status)

Span示例:

```json
{
  "trace_id": "7bba9f33312b3dbb8b2c2c62bb7abe2d",
  "parent_id": "",
  "span_id": "086e83747d0e381e",
  "name": "/v1/sys/health",
  "start_time": "2021-10-22 16:04:01.209458162 +0000 UTC",
  "end_time": "2021-10-22 16:04:01.209514132 +0000 UTC",
  "status_code": "STATUS_CODE_OK",
  "status_message": "",
  "attributes": {
    "net.transport": "IP.TCP",
    "net.peer.ip": "172.17.0.1",
    "net.peer.port": "51820",
    "net.host.ip": "10.177.2.152",
    "net.host.port": "26040",
    "http.method": "GET",
    "http.target": "/v1/sys/health",
    "http.server_name": "mortar-gateway",
    "http.route": "/v1/sys/health",
    "http.user_agent": "Consul Health Check",
    "http.scheme": "http",
    "http.host": "10.177.2.152:26040",
    "http.flavor": "1.1"
  },
  "events": [
    {
      "name": "",
      "message": "OK",
      "timestamp": "2021-10-22 16:04:01.209512872 +0000 UTC"
    }
  ]
}
```

Spans能够嵌套，通过父span ID的形式实现：子span代表子操作。这允许span捕获在应用程序更精确的工作内容。

### Span Context

Span Context在每个span中是一个不可变的对象，包含如下 内容：

* Trace ID，表示这个span所属的trace
* Span's Span ID
* Trace Flags，一个二进制编码，包含这个trace有关的信息
* Trace State，一个k-v列表，包含供应商相关的trace信息

Span Context作为span的一部分，将被序列化和伴随[Distributed Context](#context-propagation) 和 [Baggage](/docs/concepts/signals/baggage)传播。

因为Span Context包含Trace ID，它将在创建[Span Links](#span-links)时使用。

### Attributes

Attributes是一组包含元数据的k-v对，这些信息被用于携带与track操作有关的信息。

例如，如果一个span是跟踪电子商务系统中一个添加用户购物车的操作，可以捕获：用户ID、商品ID和购物车ID。

无论是哪种语言的SDK实现，Attributes有如下规则：

* Keys必须是non-null字符串
* Values必须是non-null字符串、boolean、floating、integer或者这些值的数组

另外，还有
[语义属性](/docs/reference/specification/trace/semantic_conventions/)，即通常能表示操作含义的元数据命名约定。这将有助于在各系统之间标准化这些属性。

### Span Events

Span Event可以看作结构化的日志消息（或注解），通常用来表示在span的持续期间内一个有意义的、 单个时间点。

例如，考虑在web浏览器的两个场景：

1. 跟踪网页的加载
2. 表示网页变成交互模式

Span最好被用于第一种场景，因为这个操作有带有开始和结束时间。

一个Span Event最好被用于跟踪第二种场景，因为它是一个用意义的、单个时间点。

### Span Links

Links能够将一个span关联到一个或多个span，这意味着因果关系。 例如，通过trace确定跟踪一个分布式系统中的哪些操作。

在处理一些操作的响应中，往往用一个队列实现异步处理。通过Link我们可以很好地跟踪这些后续操作。

我们希望这些后续操作与第一个trace关联，但是我们无法预测这些后续操作什么时候开始。我需要关联这两个trace，所以这是我们使用span link。

你可以连接第一个trace的最后一个span，到第二trace的第一个span。现在它们被有因果关系的关联到了一起。

Links是虽然可选项，但是它是实现span彼此关联的很好方式。

### Span Status

状态将被附加到一个span上。通常，当应用程序发生了一个已知的错误（例如异常）时，将设置span的状态。状态的取值范围如下：

- `Unset`
- `Ok`
- `Error`

当处理了一个异常，span状态被设置成 `Error`，否则为 `Unset`。当设置为 `Unset`状态，处理span的back-end将为该span指定一个最终状态。

### Span Kind

当一个span被创建，它将是 `Client`, `Server`, `Internal`, `Producer`,
或 `Consumer`中的一个。这个span kind用于提示back-end如何组装这个span。根据OpenTelemetry规范，一个 `server span`的父span经常是远端的 `client span`。类似地，一个 `consumer span`的父span总是一个 `producer`，这个 `producer`的子span总是 `consumer`。如果没有指定，span kind被认为是 `internal`。

有关span kind的更多信息，请参阅[SpanKind]({{< relref "/docs/reference/specification/trace/api#spankind" >}}).

#### Client

client span表示一个同步远程输出调用，例如一个HTTP请求或数据库调用。注意这里的“同步”不涉及 `async/await`，是指不能放入队列中后续处理。

#### Server

server span表示一个同步远程的输入调用，例如HTTP请求或远程调用。

#### Internal

Internal spans表示操作没有跨越进程边界。例如，函数调用或者中间件使用的内部span。

#### Producer

Producer spans表示任务的创建，也许后续处理的异步任务。这也许是一个远程任务，例如插入一个任务队列或通过事件监听器处理本地任务。

#### Consumer

Consumer spans表示任务的处理，而且有可能在producer已经结束以后才开始。

[traces规范]: /docs/reference/specification/overview/#tracing-signal
