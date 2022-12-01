---
title: "Instrumenting libraries"
description: >-
  学习如何在库中添加原生的度量功能
weight: 40
---
OpenTelemetry为很多库提供了度量库，通常情况是通过库的hook或monkey-patching实现。

支持OpenTelemetry的原生度量库，提供更好的可观测性和更好的开发用户体验，因为不再需要额外的库暴露和说明hooks：

- 自定义的logging hook被通用的和容易使用的OpenTelemetry API取代，这样用户只是和OpenTelemetry交互
- 来自库和应用程序代码的traces, logs, metrics被关联到一起
- 通用的约定允许用户使用相同技术，跨越库和开发语言，获得类似或一致的遥测数据
- telemetry signals能够被很好地优化（如过滤、处理、聚合）以适用于各种消费场景，因为可以使用有良好文档的各种各样的OpenTelemetry扩展点。

## Semantic Conventions

已有的语义公约[semantic
conventions]({{< relref "/docs/reference/specification/trace/semantic_conventions/" >}})，涵盖了web框架、RPC客户端、数据库、消息客户端、基础设施和其他。

如果你的库是遵循公约的，它们描述哪些信息应该包含在span中。公约使得度量工作一致：遥测数据的使用者不必学习库的规范，和可观测性供应商为广泛的技术构建使用经验（例如，数据库或消息系统）。当库遵循公约，不需要用户输入或配置的情况下，实现许多场景下开箱即用。

如果有任何反馈或者想添加新的公约，请参与和贡献！[Instrumentation Slack](https://cloud-native.slack.com/archives/C01QZFGMLQ7)或者[Specification repo](https://github.com/open-telemetry/opentelemetry-specification)是起步的一个好地方！

## 何时不需要instrument

一些库是很薄的client，仅是对网络调用的包装。OpenTelemetry已经有度量底层PRC调用的库，这种情况度量包装库不是必需的。

在如下情况下，不需要度量：

* 你的库是很薄的API代理，已经有文档或自解释的
* OpenTelemetry已经度量了底层网络调用
* 你的库没有可遵循的公约

如果不确定是否需要度量，总是可以将度量工作留在以后需要的时候做。

如果选择不度量，提供为内部RPC客户端实例配置OpenTelemetry handler的方式，仍是有用的。对于不支持全面自动度量的开发语言是至关重要的。

本文档是剩余部分是，当你决定开始度量后，指导你度量什么和如何度量。

## OpenTelemetry API

第一步添加依赖的OpenTelemetry API包。

OpenTelemetry有[两个主要模块]({{< relref "/docs/reference/specification/overview" >}}) - API和SDK。
OpenTelemetry API是一套抽象的接口和没有实际功能的实现。除非在应用程序中引入OpenTelemetry SDK，否则度量将不做任何事情，而且也不会影响应用程序的性能。

**Libraries应该只使用OpenTelemetry API.**

关注新的依赖是理所当然的事，下面的建议有助于如何确定最少的依赖：

- OpenTelemetry Trace API在2021年初已经稳定，其遵循[Semantic Versioning 2.0]({{< relref "/docs/reference/specification/versioning-and-stability" >}})，并且我们极其重视API的稳定性。
- 当需要引入API时，建议使用OpenTelemetry最早的稳定版本(1.0.*)，并且除非必须使用新特性否则应避免更新。
- 当instrumentation稳定之后，可以考虑将其变成为单独的包，这样对没有使用它的用户不会造成任何问题。你可以将其保存到你的仓库，或[add it to OpenTelemetry](https://github.com/open-telemetry/oteps/blob/main/text/0155-external-modules.md#contrib-components)，这样它可以和其他instrumentation一起工作。
- Semantic Conventions are [not stable
  yet]({{< relref "/docs/reference/specification/versioning-and-stability#not-defined-semantic-conventions-stability" >}}):
  虽然这不会造成任何功能问题，需要每隔一段时间更新你的instrumentation。在插件预览或OpenTelemetry contrib 仓库中，在不用颠覆性更改用户使用的情况下，可以帮助保持公约的最新。

### 获得tracer

通过Tracer API，所有应用配置对于你的库来说是不可见的。默认情况下，库应该从[全局的 `TracerProvider`]({{< relref "/docs/reference/specification/trace/api#get-a-tracer" >}})得到tracer。

```java
private static final Tracer tracer = GlobalOpenTelemetry.getTracer("demo-db-client", "0.1.0-beta1");
```

对于库来说，有一个允许应用程序通过API，显式地传递给 `TracerProvider`实例是很有用的，这将使得更好的依赖注入关系和更简单的测试。

当得到一个tracer之后，提供你的库(或tracing插件)的名字和版本。它们将出现在遥测数据上，帮助用户处理和过滤遥测数据，理解数据是从哪里来的，调试/报告任何instrumentation问题。

## instrument什么

![Nested database and HTTP spans in Jaeger UI](https://raw.github.com/open-telemetry/opentelemetry.io/main/iconography/Instrumenting_Library_nested_spans.svg)

### 公共APIs

trace公共APIs是一个很好的选择：为public API调用创建span，允许用户将遥测数据映射到应用程序代码中，用于了解库调用持续的时间和输出。应该被trace的调用：

- 公共方法，需要大量时间或可能失败的网络调用或本地操作(例如IO操作)
- 处理请求或消息的handler

**Instrumentation示例:**

```java
private static final Tracer tracer = GlobalOpenTelemetry.getTracer("demo-db-client", "0.1.0-beta1");

private Response selectWithTracing(Query query) {
    // check out conventions for guidance on span names and attributes
    Span span = tracer.spanBuilder(String.format("SELECT %s.%s", dbName, collectionName))
            .setSpanKind(SpanKind.CLIENT)
            .setAttribute("db.name", dbName)
            ...
            .startSpan();

    // makes span active and allows correlating logs and nest spans
    try (Scope unused = span.makeCurrent()) {
        Response response = query.runWithRetries();
        if (response.isSuccessful()) {
            span.setStatus(StatusCode.OK);
        }

        if (span.isRecording()) {
           // populate response attributes for response codes and other information
        }
    } catch (Exception e) {
        span.recordException(e);
        span.setStatus(StatusCode.ERROR, e.getClass().getSimpleName());
        throw e;
    } finally {
        span.end();
    }
}
```

请遵循公约填充属性！如果没有可用的公约，请使用[一般公约]({{< relref "/docs/reference/specification/trace/semantic_conventions/span-general" >}}).

### Nested network and other spans

通过相应的客户端实现，网络调用通常被OpenTelemetry自动监测。

如果OpenTelemetry没有支持trace你的网络客户端，考虑以下因素，对你的决策将有所帮助：

- 跟踪网络调用能提升用户的可观测性吗？你有能力支持它们吗？
- 你的库是对公共的、文档化的RPC API的封装吗？万一出现问题，用户需要从底层service得到支持吗？
  - 对库进行监测，确保trace每次网络尝试
- 使用span跟踪这些调用会非常冗长吗？会显著影响性能吗？
  - 使用带细节或span事件的log：当在span事件设置公共API span时，log可以关联到父日志（公共API调用） 。
  - 如果不得不span（用于携带和传播唯一的trace上下文），请使用配置选项控制它们开关，并且默认是禁止的。

如果OpenTelemetry已经支持trace你的网络调用，你可能不想再重复造轮子。当然也一些例外：

- 为了支持不能自动监测的用户（在某些环境自动监测无法工作，或用户在意monkey-patching）
- 为了使用自定义（传统）关联和上下文传播协议
- 在RPC span中增加库或service至关重要的特定信息，这些信息自动监测没有覆盖。

警告: 避免重复的通用解决方案正在构建中 🚧。

### Events

Trace是应用程序发出的一种signal。event（或者叫做log）和trace彼此之间是互补关系，而非重复。无论何时如果想获得详尽的信息，log总是优于trace。

如果你的应用已经使用了log或其他类似模块，也许已经有可以和OpenTelemetry集成了，可以参考[registry](/registry/)。集成的方式通常是在所有log中增加活跃状态的trace的上下文，以致用户可以关联它们。

如果你的开发语言和生态不支持通用的log，请使用[span event][]共享额外的应用程序详细信息。比起在span中添加属性，事件也许更便捷。

一般来说，详细信息使用event或log，而不使用span。请在创建span的时候添加event，要避免在一个活跃的span做这样的事。因为对于一个活跃的span来说，你可能不能控制它指向什么。

## Context propagation

### 提取context

如果一个库或service收到了上游调用（例如web框架或者消息消费者），需要从收到的请求/消息这提取上下文。OpenTelemetry提供了 `Propagator` API，它隐藏了具体的传播标准，并且从连接中读取trace的上下文。在单一应答的情况下，从连接中读到上下文，将是新span的父span。

当创建一个span之后，应该向应用程序传递一个新的trace上下文（callback或handler），标识当前span是活跃的。如果可能的话，需要显式做这些事。

```java
// extract the context
Context extractedContext = propagator.extract(Context.current(), httpExchange, getter);
Span span = tracer.spanBuilder("receive")
            .setSpanKind(SpanKind.SERVER)
            .setParent(extractedContext)
            .startSpan();

// make span active so any nested telemetry is correlated
try (Scope unused = span.makeCurrent()) {
  userCode();
} catch (Exception e) {
  span.recordException(e);
  span.setStatus(StatusCode.ERROR);
  throw e;
} finally {
  span.end();
}
```

这里有一个[java语言提取上下文的完整示例](/docs/instrumentation/java/manual/#context-propagation)，其他开发语言请查阅OpenTelemetry documentation。

如果是消息系统，也许一次收到多条消息。在创建span时将收到的消息作为一个[_links_](/docs/instrumentation/java/manual/#create-spans-with-links) 。更多细节请参见 [消息公约
conventions]({{< relref "/docs/reference/specification/trace/semantic_conventions/messaging" >}})
 (警告: 消息公约还是[没有正式发布](https://github.com/open-telemetry/oteps/pull/173) 🚧).

### 注入上下文

当调用外部请求时，通常需要将上下文传递到下游service中。在这种情况下，应该使用 `Propagator` API将上下文注入到消息中，创建一个新的span用于trace外部调用。也许还有另外一种情况需要注入上下文，例如中异步处理系统中创建消息。

```java
Span span = tracer.spanBuilder("send")
            .setSpanKind(SpanKind.CLIENT)
            .startSpan();

// make span active so any nested telemetry is correlated
// even network calls might have nested layers of spans, logs or events
try (Scope unused = span.makeCurrent()) {
  // inject the context
  propagator.inject(Context.current(), transportLayer, setter);
  send();
} catch (Exception e) {
  span.recordException(e);
  span.setStatus(StatusCode.ERROR);
  throw e;
} finally {
  span.end();
}
```

可以参考这个[Java语言的注入上下文的完整示例](/docs/instrumentation/java/manual/#context-propagation).

也许有一些例外情况，如下：

- 下游service不支持元数据或禁止未知字段
- 下游service没有定义关联协议。假如将来的版本支持，建议还是注入上下文！
- 下游service支持定义的关联协议。
  - 请尽最大的努力与OpenTelemetry保持兼容。
  - 或者在span上创建和标记自定义关联ID。

### In-process

- **让span处于活跃状态** (即current)：使得log和任何自动监视器产生关联。
- 如果库有上下文的概念，除了活跃span以外，支持可选的明确上下文传播
  - 将库创建的span(trace上下文) 明确地放入上下文中，并说明如何访问它c
  - 允许在你的上下文中传递trace上下文
- 在库中，显式传播的trace上下文，也许在callback期间发生改变！
  - 一旦从公共API中捕捉到活跃的上下文，立刻用作你span的父上下文
  - 传递上下文和标记属性、异常、显式传递实例事件
  - 如果你启动了线程、后台处理或其他由于异步打断上下文流程的限制，这是至关重要的。

## Metrics

[Metrics API]({{< relref "/docs/reference/specification/metrics/api" >}}) 还没有稳定，也没有定义metrics的公约。

## Misc

### Instrumentation registry

请将你开发的监测库，添加到[OpenTelemetry registry](/registry/)，这样其他使用者可以找到它。

### 性能Performance

当在应用程序在没有SDK时，OpenTelemetry API不做任何操作，有非常好的性能。当配置SDK后，它 [消耗有限的资源]({{< relref "/docs/reference/specification/performance" >}})。

现实环境的应用程序，尤其在规模很大的时候，经常配置为基于头部采样。采样外的span代价非常低，可以通过检查span是否需要记录，从而避免额外的内存分配和潜在昂贵的计算。

```java
// some attributes are important for sampling, they should be provided at creation time
Span span = tracer.spanBuilder(String.format("SELECT %s.%s", dbName, collectionName))
        .setSpanKind(SpanKind.CLIENT)
        .setAttribute("db.name", dbName)
        ...
        .startSpan();

// other attributes, especially those that are expensive to calculate
// should be added if span is recording
if (span.isRecording()) {
    span.setAttribute("db.statement", sanitize(query.statement()))
}
```

### Error处理

OpenTelemetry API在[运行时忽略错误]({{< relref "/docs/reference/specification/error-handling#basic-error-handling-principles" >}}) ，也就是说即使遇到非法参数时也不会失败，从来不抛异常。这种方式检测的问题不会影响应用程序的逻辑。在测试监测程序时要注意OpenTelemetry在运行时隐藏的问题。

### 测试

由于OpenTelemetry有各种各样的自动监测器，尝试如何使你的监测器与其他监测器配合工作是很有价值的，如：输入请求、输出请求、log等。基于流行框架和库的开发的典型应用程序，当试着使用你的监测器是能所有trace时，参考一下和你类似库是如何做的。

为了单元测试，通常需要mock或fake `SpanProcessor` 和 `SpanExporter`。

```java
@Test
public void checkInstrumentation() {
  SpanExporter exporter = new TestExporter();

  Tracer tracer = OpenTelemetrySdk.builder()
           .setTracerProvider(SdkTracerProvider.builder()
              .addSpanProcessor(SimpleSpanProcessor.create(exporter)).build()).build()
           .getTracer("test");
  // run test ...

  validateSpans(exporter.exportedSpans);
}

class TestExporter implements SpanExporter {
  public final List<SpanData> exportedSpans = Collections.synchronizedList(new ArrayList<>());

  @Override
  public CompletableResultCode export(Collection<SpanData> spans) {
    exportedSpans.addAll(spans);
    return CompletableResultCode.ofSuccess();
  }
  ...
}
```

[instrumentation libraries]: /docs/reference/specification/overview/#instrumentation-libraries
[span events]: /docs/reference/specification/trace/api/#add-events
