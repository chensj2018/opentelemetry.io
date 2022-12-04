---
title: Getting Started
weight: 2
---
欢迎来到OpenTelemetry C++开始指南！将引导你完成基本步骤：安装、指标监测、配置和从OpenTelemetry导出数据。

可以使用[CMake](https://cmake.org/) 或 [Bazel](https://bazel.build/) 编译OpenTelemetry C++。接下来指南在将使用CMake，提供让应用程序（一个http server和http client）运行的最基本的步骤。更多信息，请参阅[these instructions](https://github.com/open-telemetry/opentelemetry-cpp/blob/main/INSTALL.md)。

## 先决条件

在Windows, macOS 或 Linux编译OpenTelemetry C++。 首先需要安装如下依赖：

{{< ot-tabs "Linux (apt)" "Linux (yum)" "MacOS (homebrew)">}}
{{< ot-tab lang="shell">}}
$ sudo apt-get install git cmake g++ libcurl4-openssl-dev
{{< /ot-tab >}}
{{< ot-tab lang="shell">}}
$ sudo yum install git cmake g++ libcurl-devel
{{< /ot-tab >}}
{{< ot-tab lang="shell">}}
$ xcode-select —install
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
$ brew install git cmake
{{< /ot-tab >}}
{{< /ot-tabs >}}

## 编译

获取opentelementry-cpp源码:

```shell
$ git clone --recursive https://github.com/open-telemetry/opentelemetry-cpp
```

导航到上面克隆的仓库上，创建CMake编译配置：

```shell
$ cd opentelemetry-cpp
$ mkdir build && cd build
$ cmake -DBUILD_TESTING=OFF ..
```

配置文件创建之后，编译CMake工程，生成 `http_client` 和 `http_server`:

```shell
cmake --build . --target http_client http_server
```

完成之后在 ` ./expamples/http`目录下，可以找到二进制执行文件： `http_server `and `http_client `

```shell
$ ls ./examples/http
CMakeFiles  Makefile  cmake_install.cmake  http_client  http_server
```

## 运行应用程序

打开两个终端，第一运行http server：

```shell
$ ./examples/http/http_server
Server is running..Press ctrl-c to exit..
```

另外一个，一下http client：

```shell
$ ./examples/http/http_client
{
  name          : /helloworld
  trace_id      : 05eec7a55d3544434265dad89d7fe96f
  span_id       : 45fb62c58c907f05
  tracestate    :
  parent_span_id: 0000000000000000
  start         : 1665577080650384378
  duration      : 1640298
  description   :
  span kind     : Client
  status        : Unset
  attributes    :
    http.header.Date: Wed, 12 Oct 2022 12:18:00 GMT
    http.header.Content-Length: 0
    http.status_code: 200
    http.method: GET
    .header.Host: localhost
    http.header.Content-Type: text/plain
    http.header.Connection: keep-alive
    .scheme: http
    http.url: http://localhost:8800/helloworld
  events        :
  links         :
  resources     :
    service.name: unknown_service
    telemetry.sdk.version: 1.6.1
    telemetry.sdk.name: opentelemetry
    telemetry.sdk.language: cpp
  instr-lib     : http-client
}
```

在server端，trace被输出到了控制台：

```shell
{
  name          : /helloworld
  trace_id      : 05eec7a55d3544434265dad89d7fe96f
  span_id       : 8df967d8547813fe
  tracestate    :
  parent_span_id: 45fb62c58c907f05
  start         : 1665577080651459495
  duration      : 46331
  description   :
  span kind     : Server
  status        : Unset
  attributes    :
    http.header.Traceparent: 00-05eec7a55d3544434265dad89d7fe96f-45fb62c58c907f05-01
    http.header.Accept: */*
    http.request_content_length: 0
    http.header.Host: localhost:8800
    http.scheme: http
    http.client_ip: 127.0.0.1:49466
    http.method: GET
    net.host.port: 8800
    net.host.name: localhost
  events        :
    {
      name          : Processing request
      timestamp     : 1665577080651472827
      attributes    :
    }
  links         :
  resources     :
    service.name: unknown_service
    telemetry.sdk.version: 1.6.1
    telemetry.sdk.name: opentelemetry
    telemetry.sdk.language: cpp
  instr-lib     : http-server
}
```

## 接下来做什么

在代码中使用[手动](/docs/instrumentation/cpp/manual)的方式，丰富自动生成的监测指标。这将得到自定义的可观测数据。

也许你想配置一个适当的exporter，将[远程监测数据导出](/docs/instrumentation/cpp/exporters)到一个或多个遥测的back-end。
