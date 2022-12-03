---
title: "General SDK Configuration"
description: >-
 General-purpose environment variables for configuring an OpenTelemetry SDK.
weight: 1
---
## `OTEL_SERVICE_NAME`

设置资源属性[`service.name`](/docs/reference/specification/resource/semantic_conventions/#service) 的名字。

**默认值:** `"unknown_service"`

如果 `service.name` 在 `OTEL_RESOURCE_ATTRIBUTES`里提供, 环境变量 `OTEL_SERVICE_NAME` 将被采用。

**示例:**

`export OTEL_SERVICE_NAME="your-service-name"`

## `OTEL_RESOURCE_ATTRIBUTES`

被用于资源属性的Key-value键值对。更多信息，参见 [Resource SDK](/docs/reference/specification/resource/sdk#specifying-resource-information-via-an-environment-variable)。

**默认值:** Empty.

普通资源类型遵循的语义规范，参见 [Resource semantic conventions](/docs/reference/specification/resource/semantic_conventions/#semantic-attributes-with-sdk-provided-default-value)

**示例:**

`export OTEL_RESOURCE_ATTRIBUTES="key1=value1,key2=value2"`

## `OTEL_TRACES_SAMPLER`

指定SDK使用的采样器。

**默认值:** `"parentbased_always_on"`

**示例:**

`export OTEL_TRACES_SAMPLER="traceidratio"`

 `OTEL_TRACES_SAMPLER` 取值范围如下:

- `"always_on"`: `AlwaysOnSampler`
- `"always_off"`: `AlwaysOffSampler`
- `"traceidratio"`: `TraceIdRatioBased`
- `"parentbased_always_on"`: `ParentBased(root=AlwaysOnSampler)`
- `"parentbased_always_off"`: `ParentBased(root=AlwaysOffSampler)`
- `"parentbased_traceidratio"`: `ParentBased(root=TraceIdRatioBased)`
- `"parentbased_jaeger_remote"`: `ParentBased(root=JaegerRemoteSampler)`
- `"jaeger_remote"`: `JaegerRemoteSampler`
- `"xray"`: [AWS X-Ray Centralized
  Sampling](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html)
  (_third party_)

## `OTEL_TRACES_SAMPLER_ARG`

如果 `OTEL_TRACES_SAMPLER`的采样器可用，通过该环境变量指定采样器所需的参数。该值只能被 `OTEL_TRACES_SAMPLER` 使用。每种采样器类型都有其期望的输入参数。无效或不可识别的输入参数将记录错误日志。

**默认值:** Empty.

**示例:**

```shell
export OTEL_TRACES_SAMPLER="traceidratio"
export OTEL_TRACES_SAMPLER_ARG="0.5"
```

依赖于 `OTEL_TRACES_SAMPLER`设置的值， `OTEL_TRACES_SAMPLER_ARG` 也许被设置为如下值：

- 对于 `traceidratio` 和 `parentbased_traceidratio` ：采样概率，取值的[0..1]之间，例如"0.25"。默认值为1.0。
- 对于 `jaeger_remote` 和 `parentbased_jaeger_remote`: 是","分隔的列表：
  - 例如:
    `"endpoint=http://localhost:14250,pollingIntervalMs=5000,initialSamplingRate=0.25"`
  - `endpoint`: gRPC服务端 `scheme://host:port`形式的接入点，该gPRC服务端提供服务的采样策略([sampling.proto](https://github.com/jaegertracing/jaeger-idl/blob/master/proto/api_v2/sampling.proto))。
  - `pollingIntervalMs`:  单位毫秒，指示采样器从后端更新采样策略的频率。
  - `initialSamplingRate`:  取值在 [0..1]之间，当后端不可用时使用的采样概率。一旦从后端获取到了采样策略，该值的作用将失效，从后端获取的策略将一直使用到下一次更新。

## `OTEL_PROPAGATORS`

指定使用的Propagators，是通过","分隔的列表。 

**默认值:** `"tracecontext,baggage"

**示例:**

`export OTEL_PROPAGATORS="b3"`

Accepted values for `OTEL_PROPAGATORS` are:

- `"tracecontext"`: [W3C Trace Context](https://www.w3.org/TR/trace-context/)
- `"baggage"`: [W3C Baggage](https://www.w3.org/TR/baggage/)
- `"b3"`: [B3 Single](/docs/reference/specification/context/api-propagators#configuration)
- `"b3multi"`: [B3 Multi](/docs/reference/specification/context/api-propagators#configuration)
- `"jaeger"`: [Jaeger](https://www.jaegertracing.io/docs/1.21/client-libraries/#propagation-format)
- `"xray"`: [AWS
  X-Ray](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-tracingheader)
  (_third party_)
- `"ottrace"`: [OT
  Trace](https://github.com/opentracing?q=basic&type=&language=) (_third party_)
- `"none"`: 没有配置自动propagator.

## `OTEL_TRACES_EXPORTER`

指定trace使用的exporter。

**默认值:** `"otlp"`

**示例:**

`export OTEL_TRACES_EXPORTER="jaeger"`

取值范围如下：

- `"otlp"`: [OTLP][spec-otlp]
- `"jaeger"`: 导出Jaeger数据格式
- `"zipkin"`: [Zipkin](https://zipkin.io/zipkin-api/)
- `"none"`:没有配置Exporter

## `OTEL_METRICS_EXPORTER`

指定metrics的exporter

**默认值:** `"otlp"`

**示例:**

`export OTEL_METRICS_EXPORTER="prometheus"`

 `OTEL_METRICS_EXPORTER` 的取值范围如下：

- `"otlp"`: [OTLP][spec-otlp]
- `"prometheus"`: [Prometheus](https://github.com/prometheus/docs/blob/master/content/docs/instrumenting/exposition_formats.md)
- `"none"`: 没有配置 metrics的exporter

## `OTEL_LOGS_EXPORTER`

指定logs使用的exporter

**默认值:** `"otlp"`

**示例:**

`export OTEL_LOGS_EXPORTER="otlp"`

 `OTEL_LOGS_EXPORTER` 取值范围:

- `"otlp"`: [OTLP][spec-otlp]
- `"none"`: 没有配置logs的exporter

[spec-otlp]: /docs/reference/specification/protocol/otlp
