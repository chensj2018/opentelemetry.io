---
依赖于SDK，但是通常是 `http/protobuf` 或 `grpc`依赖于SDK，但是通常是 `http/protobuf` 或 `grpc`title: "OTLP Exporter Configuration"
description: >-
 配置OTLP Exporter的环境变量
weight: 2
---
## Endpoint Configuration

通过下面的环境变量，可以配置OTLP/gRPC或OTLP/HTTP的接入点，这些接入点用于traces, metrics, and logs。

### `OTEL_EXPORTER_OTLP_ENDPOINT`

用于任何signal类型的基本endpoint URL，有助于将多个signal发送到相同的endpoint，而且希望使用一个环境变量控制这个endpoint。

**默认值:**

* gRPC: `"http://localhost:4317"`
* HTTP: `"http://localhost:4318"`

**示例:**

* gRPC: `export OTEL_EXPORTER_OTLP_ENDPOINT="my-api-endpoint:443"`
* HTTP: `export OTEL_EXPORTER_OTLP_ENDPOINT="http://my-api-endpoint/"`

对于OTLP/HTTP，当该环境变量被设置SDK中的exporters将构建与signal相关的URLs。这意味着将发送traces，metrics和log到如下的URLs：

* Traces: `"http://my-api-endpoint/v1/traces"`
* Metrics: `"http://my-api-endpoint/v1/metrics"`
* Logs: `"http://my-api-endpoint/v1/logs"`

### `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT`

trace数据的Endpoint URL。如果是OTLP/HTTP必须是以 `v1/traces`结尾。

**默认值:**

* gRPC: `"http://localhost:4317"`
* HTTP: `"http://localhost:4318/v1/traces"`

**示例:**

* gRPC: `export OTEL_EXPORTER_OTLP_TRACES_ENDPOINT="my-api-endpoint:443"`
* HTTP:`export OTEL_EXPORTER_OTLP_TRACES_ENDPOINT="http://my-api-endpoint/v1/traces"`

### `OTEL_EXPORTER_OTLP_METRICS_ENDPOINT`

metric数据的Endpoint URL，如果是OTLP/HTTP必须是以 `v1/metrics`结尾。

**默认值:**

* gRPC: `"http://localhost:4317"`
* HTTP: `"http://localhost:4318/v1/metrics"`

**示例:**

* gRPC: `export OTEL_EXPORTER_OTLP_METRICS_ENDPOINT="my-api-endpoint:443"`
* HTTP:`export OTEL_EXPORTER_OTLP_METRICS_ENDPOINT="http://my-api-endpoint/v1/metrics"`

### `OTEL_EXPORTER_OTLP_LOGS_ENDPOINT`

log数据的Endpoint URL，如果是OTLP/HTTP必须是以 `v1/logs`结尾。

**默认值:**

* gRPC: `"http://localhost:4317"`
* HTTP: `"http://localhost:4318/v1/logs"`

**示例:**

* gRPC: `export OTEL_EXPORTER_OTLP_LOGS_ENDPOINT="my-api-endpoint:443"`
* HTTP:`export OTEL_EXPORTER_OTLP_LOGS_ENDPOINT="http://my-api-endpoint/v1/logs"`

## Header configuration

通过下面的环境变量，可以在gRPC或HTTP请求header中配置额外的key-value键值对。

### `OTEL_EXPORTER_OTLP_HEADERS`

在所有输出数据(traces, metrics, and logs)上添加的header列表。

**默认值:** N/A

**示例:**

`export OTEL_EXPORTER_OTLP_HEADERS="api-key=key,other-config-value=value"`

### `OTEL_EXPORTER_OTLP_TRACES_HEADERS`

在输出trace上的添加的header列表

**默认值:** N/A

**示例:**

`export OTEL_EXPORTER_OTLP_TRACES_HEADERS="api-key=key,other-config-value=value"`

### `OTEL_EXPORTER_OTLP_METRICS_HEADERS`

在输出metrics上的添加的header列表

**默认值:** N/A

**示例:**

`export OTEL_EXPORTER_OTLP_METRICS_HEADERS="api-key=key,other-config-value=value"`

### `OTEL_EXPORTER_OTLP_LOGS_HEADERS`

在输出logs上的添加的header列表

**默认值:** N/A

**示例:**

`export OTEL_EXPORTER_OTLP_LOGS_HEADERS="api-key=key,other-config-value=value"`

## Timeout Configuration

通过下面的环境变量，配置OTLP exporter在发送网络数据批之前，等待的最大时间（单位毫秒）。

### `OTEL_EXPORTER_OTLP_TIMEOUT`

所有输出数据(traces, metrics, and logs)的超时时间，单位毫秒。

**默认值:** `10000` (10s)

**示例:**

`export OTEL_EXPORTER_OTLP_TIMEOUT=500`

### `OTEL_EXPORTER_OTLP_TRACES_TIMEOUT`

所有输出traces数据的超时时间，单位毫秒。

**默认值:** 10000 (10s)

**示例:**

`export OTEL_EXPORTER_OTLP_TRACES_TIMEOUT=500`

### `OTEL_EXPORTER_OTLP_METRICS_TIMEOUT`

所有输出metrics数据的超时时间，单位毫秒。

**默认值:** 10000 (10s)

**示例:**

`export OTEL_EXPORTER_OTLP_METRICS_TIMEOUT=500`

### `OTEL_EXPORTER_OTLP_LOGS_TIMEOUT`

所有输出logs数据的超时时间，单位毫秒。

**默认值:** 10000 (10s)

**示例:**

`export OTEL_EXPORTER_OTLP_LOGS_TIMEOUT=500`

### `OTEL_EXPORTER_OTLP_PROTOCOL`

指定远程监测数据使用的OTLP传输协议。

**默认值:** 依赖于SDK，但是通常是 `http/protobuf` 或 `grpc`

**示例:**

`export OTEL_EXPORTER_OTLP_PROTOCOL=grpc`

有效值，如下:

* `grpc` to use OTLP/gRPC
* `http/protobuf` to use OTLP/HTTP+protobuf
* `http/json` to use OTLP/HTTP+json

### `OTEL_EXPORTER_OTLP_TRACES_PROTOCOL`

指定trace使用的OTLP传输协议

**默认值:** 依赖于SDK，但是通常是 `http/protobuf` 或 `grpc`

**示例:**

`export OTEL_EXPORTER_OTLP_TRACES_PROTOCOL=grpc`

有效值，如下:

* `grpc` to use OTLP/gRPC
* `http/protobuf` to use OTLP/HTTP+protobuf
* `http/json` to use OTLP/HTTP+json

### `OTEL_EXPORTER_OTLP_METRICS_PROTOCOL`

指定metrics使用的OTLP传输协议

**默认值:** SDK-dependent, but will typically be either `http/protobuf` or `grpc`.

**示例:**

`export OTEL_EXPORTER_OTLP_METRICS_PROTOCOL=grpc`

有效值，如下:

* `grpc` to use OTLP/gRPC
* `http/protobuf` to use OTLP/HTTP+protobuf
* `http/json` to use OTLP/HTTP+json
