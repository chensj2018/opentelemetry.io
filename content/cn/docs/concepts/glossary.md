---
title: Glossary
description: >-
 OpenTelemetry项目使用的术语
weight: 100
---
OpenTelemetry项目使用的术语，你也许熟悉或者不熟悉。
另外，项目也许定义的术语含义和其他地方不同。本章罗列了项目中使用的术语以及其含义。

## 通用术语

### **Aggregation**

在程序执行期间，综合一段时间间隔内的产生的多项指标监测数据，得到精确或估计的统计值。该值被[`Metric`](#metric) [`Data Source`](#data-source)使用。

### **API**

应用程序接口。在OpenTelemetry项目中，被用于定义[`Data Source`](#data-source)如何产生远程监测数据。

### **Application**

为终端用户或其他应用设计的一个或多个[`Services`](#service) 。

### **APM**

Application Performance Monitoring是用于监测软件应用程序的性能（速度、可靠性、可用性等），以便发现问题、产生告警和定位问题根本原因。

### **Attribute**

一个A key-value键值对。用于跨越远程监控signals。例如，在[`Traces`](#trace)的[`Span`](#span)中附加数据,或者在 [`Metrics`](#metric)。参见[attribute][attribute]。

### **Automatic Instrumentation**

是指收集远程监测数据的方法，该方法不需要终端用户修改应用程序的代码。不同的编程语言这些方法有很大的不同，例如包括字节码注入或者monkey patching。

### **Baggage**

传播name/value键值对的机制，用于在event和service之间建立因果关系，参见[baggage spec][baggage]。

### **Client Library**

参见[`Instrumented Library`](#instrumented-library)。

### **Client-side App**

[`Application`](#application)的一个组件，一般情况由终端用户直接使用。例如，浏览器app、移动端app或运行在IoT设备上的app。

### **Collector**

如何接收、处理和导出远程监测数据的实现方式，并且这种实现方式是与厂商无关的。是一个单一的二进制包，可以部署为agent或gateway。

也被称为OpenTelemetry Collector，有关Collector的更多信息请参阅[这里][collector]。

### **Contrib**

数个[`指标监测库`](#instrumentation-library)和[`Collector`](#collector)，提供一套核心能力以及包括商业 `Exporter`的非核心代码库。

### **Context Propagation**

允许所有[`Data Sources`](#data-source)为了存储状态和跨[`Transaction`](#transaction)生命周期访问数据，分享上下文的机制。参见[context propagation][context propagation]。

### **DAG**

有向无环图[Directed Acyclic Graph][dag].

### **Data Source**

参见 [`Signal`](#signal)

### **Dimension**

参见 [`Label`](#label).

### **Distributed Tracing**

跟踪单一 ` Request`的流程称之为 ` Trace`，它由组成 `Application`的 `Services`处理。一个分布式trace跨越了进程、网络和安全边界。

有关分布式tracing的更多信息，请点击[这里][distributed tracing]。

### **Event**

表示在[`Data Source`](#data-source)上发生的事情. 例如, [`Spans`](#span).

### **Exporter**

为消费者提供发送远程监测数据的功能。被[`指标监测库`](#instrumentation-library)和[`Collector`](#collector)使用。 Exporters支持push和pull两种方式。

### **Field**

添加到[`Log Records`](#log-record)上的Name/value键值对 (类似于[`Spans`](#span)的
[`Attributes`](#attribute)和[`Metrics`](#metric)的[`Labels`](#label)。参见[field spec][field].

### **gRPC**

高性能，开源的通用[`RPC`](#rpc)框架。更多gRPC信息，请点击[这里](https://grpc.io)。

### **HTTP**

[Hypertext Transfer Protocol][http]的缩写。

### **Instrumented Library**

表示采集远程监测signals([`Traces`](#trace), [`Metrics`](#metric), [`Logs`](#log))的[`Library`](#library)。 更多信息[参见][spec-instrumented-lib]。

### **Instrumentation Library**

表示为指定[`Instrumented Library`](#instrumented-library)提供指标采集的[`Library`](#library)。如果是内嵌在OpenTelemetry instrumentation内，[`Instrumented Library`](#instrumented-library)和[`Instrumentation Library`](#instrumentation-library) 也许是相同的[`Library`](#library)。更多信息[参见][spec-instrumentation-lib]。

### **JSON**

[JavaScript Object Notation][json]的缩写。

### **Label**

参见[Attribute](#attribute).

### **Language**

编程语言。

### **Library**

使用明确的编程语言，对接口的实现集合。

### **Log**

有时指[`Log Records`](#log-record)的集合，有时指单个[`Log Record`](#log-record)。因此这个术语要谨慎使用，在可能引起歧义的场景，应该添加限定词(例如 `Log Record`)。更多信息，[参见][log]。

### **Log Record**

一个[`Event`](#event)记录。通常包括表示[`Event`](#event)何时发生的时间戳和描述发生了什么、在哪发生等信息的其他数据。更多信[参见][log record]。

### **Metadata**

添加到远程监测数据上的name/value键值对。OpenTelemetry在[`Spans`](#span)上称之为[`Attributes`](#attribute)，在[`Metrics`](#metric)上称之为[`Labels`](#label)，在[`Logs`](#log)上称之为[`Fields`](#field)。

### **Metric**

记录一个数据点，可表示原始测量值或预定义聚合后的值，看作一个带有 [`Metadata`](#metadata)信息的时间序列。更多信息，[参见][metric].

### **OC**

[`OpenCensus`](#opencensus)的缩写。

### **OpenCensus**

各种编程语言的一组库，用于收集应用程序的metric和分布式trace，然后将其发送到你选择的back-end。
[OpenTelemetry的前身](/docs/concepts/what-is-opentelemetry/#so-what)，更多信息，[参见][opencensus]。

### **OpenTracing**

厂商中立的APIs和分布式tracing的instrumentation 。
[OpenTelemetry的前身](/docs/concepts/what-is-opentelemetry/#so-what)。更多信息，[参见][opentracing].

### **OT**

[`OpenTracing`](#opentracing)的缩写。

### **OTel**

[OpenTelemetry](/docs/concepts/what-is-opentelemetry)的缩写。

### **OTelCol**

[OpenTelemetry Collector](#collector)的缩写。

### **OTLP**

[OpenTelemetry Protocol](/docs/reference/specification/protocol/otlp/)的缩写。

### **Processor**

在接收和导出之间，对数据执行的操作。例如，批处理。被
[&#39;Instrumentation Libraries&#39;](#instrumentation-library) 和
[Collector](/docs/collector/configuration/#processors)使用。

### **Propagators**

用于序列化和反序列化远程监测数据的特定部分，例如 [`Spans`](#span)的上下文和[`Baggage`]。更多信息，[参见][propagators].

### **Proto**

与编程语言无关的接口类型。更多信息，[参见][proto]。

### **Receiver**

[`Collector`](/docs/collector/configuration/#receivers)使用的术语，定义远程遥测数据如何被接收。Receivers可以是push或pull。更多信息，[参见][receiver]。

### **Request**

参见[`Distributed Tracing`](#distributed-tracing).

### **Resource**

捕获有关记录远程监测实体的信息。例如，一个运行在kubernetes容器上的被远程监测的进程，有一个pod名字，在一个namespace里，也许还属于一个deployment，这个deployment也有名字。这三个属性值被包含在 `Resource`内，并被应用到data source上。

### **REST**

[Representational State Transfer][rest]缩写。

### **RPC**

Short for [Remote Procedure Call][rpc].

### **Sampling**

控制数据导出数量的机制。最常见的用法是和[`Tracing`](#trace) [`Data Source`](#data-source)一起使用。更多信息[参见][sampling].

### **SDK**

Software Development Kit的缩写。指telemetry SDK，表示实现 OpenTelemetry [`API`](#api)的[`Library`](#library)。

### **Semantic Conventions**

为了提供厂商中立的远程监测数据，而定义的[`Metadata`](#metadata)标准名字和取值。

### **Service**

[`Application`](#application)的一个组件。为了高可用和扩展通常一个[`Service`](#service)部署多个实例。[`Service`](#service)也许被部署到多个位置。

### **Signal**

[`Traces`](#trace), [`Metrics`](#metric) 或 [`Logs`](#log)之一。更多有关Signals的信息，请查看[这里][signals]。

### **Span**

表示 [`Trace`](#trace)里的一个操作。更多信息，[参见][span].

### **Span Link**

有因果关系的span之间的链接。更多信息，请参考[Links between spans](/docs/reference/specification/overview#links-between-spans)
和
[Specifying Links](/docs/reference/specification/trace/api#specifying-links)。

### **Specification**

描述对所有实现跨编程语言的需求和期望。[参见][specification].

### **Status**

操作的结果。通常用于指示是否发生错误。[参见][status].

### **Tag**

参考[`Metadata`](#metadata).

### **Trace**

一个[`Spans`](#span)的[`DAG`](#dag), 边是由parent/child定义。[参见][trace].

### **Tracer**

负责创建[`Spans`](#span)。[参见][tracer].

### **Transaction**

参见[`Distributed Tracing`](#distributed-tracing).

### **zPages**

进程内置的exporter。当被包含到开发代码中，它将在后台收集和聚集tracing和metrics信息。这些信息服务于网页请求。更多信息[参阅][zpages]。

## 其他术语

### Traces

#### **[Trace API Terminology](/docs/reference/specification/trace/api)**

#### **[Trace SDK Terminology](/docs/reference/specification/trace/sdk)**

### Metrics

#### **[Metric API Terminology](/docs/reference/specification/metrics/api#overview)**

#### **[Metric SDK Terminology](/docs/reference/specification/metrics#specifications)**

### Logs

#### **[Trace Context Fields](/docs/reference/specification/logs/data-model#trace-context-fields)**

#### **[Severity Fields](/docs/reference/specification/logs/data-model#severity-fields)**

#### **[Log Record Fields](/docs/reference/specification/logs/data-model#log-and-event-record-definition)**

### Semantic Conventions

#### **[Resource Conventions](/docs/reference/specification/resource/semantic_conventions)**

#### **[Span Conventions](/docs/reference/specification/trace/semantic_conventions)**

#### **[Metric Conventions](/docs/reference/specification/metrics/semantic_conventions)**

[baggage]: /docs/reference/specification/baggage/api/
[attribute]: /docs/reference/specification/common/#attributes
[collector]: /docs/collector
[context propagation]: /docs/reference/specification/overview#context-propagation
[dag]: https://en.wikipedia.org/wiki/Directed_acyclic_graph
[distributed tracing]: /docs/concepts/signals/traces/
[field]: /docs/reference/specification/logs/data-model#field-kinds
[http]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol
[json]: https://en.wikipedia.org/wiki/JSON
[log]: /docs/reference/specification/glossary#log
[log record]: /docs/reference/specification/glossary#log-record
[metric]: /docs/concepts/signals/metrics/
[opencensus]: https://opencensus.io
[opentracing]: https://opentracing.io
[propagators]: /docs/instrumentation/go/manual/#propagators-and-context
[proto]: https://github.com/open-telemetry/opentelemetry-proto
[receiver]: /docs/collector/configuration/#receivers
[rest]: https://en.wikipedia.org/wiki/Representational_state_transfer
[rpc]: https://en.wikipedia.org/wiki/Remote_procedure_call
[sampling]: /docs/reference/specification/trace/sdk#sampling
[signals]: /docs/concepts/signals/
[span]: /docs/reference/specification/trace/api#span
[spans]: /docs/reference/specification/trace/api#add-events
[spec-exporter-lib]: /docs/reference/specification/glossary/#exporter-library
[spec-instrumentation-lib]: /docs/reference/specification/glossary/#instrumentation-library
[spec-instrumented-lib]: /docs/reference/specification/glossary/#instrumented-library
[specification]: /docs/concepts/components/#specification
[status]: /docs/reference/specification/trace/api#set-status
[trace]: /docs/reference/specification/overview#traces
[tracer]: /docs/reference/specification/trace/api#tracer
[zpages]: https://github.com/open-telemetry/opentelemetry-specification/blob/main/experimental/trace/zpages.md
