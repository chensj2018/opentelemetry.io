---
title: "Metrics"
description: >-
  metric是对service的测量，在运行时捕获。
weight: 2
---
**metric** 是对service的测量，在运行时捕获。从逻辑上讲，这些测量的捕获时刻称之为*metric event*，由测量值本身以及捕获的时间戳和相关的元数据。

应用程序和请求的metric是重要的可用性和性能的重要指标。自定义metric能够提供如何影响用户体验和业务的洞察。被收集的这些数据能被用于产生告警，或触发调度决策以实现高度自治的自动扩缩容。

今天OpenTelemetry定义了三种 _metric instruments_ :

- **counter**: 随着时间增加的值 -- 可用想象为汽车的里程表，它只能不断增加。
- **measure**: 随着时间聚集的值，这不像汽车里程表，而是表示一定范围内的值。
- **observer**: 在某个时间点上捕获的一组值。就像汽车里的燃油表。

除了在这三个metric指标以外，_aggregations_ 也是理解metric的一个重要的概念。aggregation是一种技术，将在发生metric事件的时间窗口内的大量测量数据，合并成精确或估计的统计数值。OpenTelemetry API本身不允许指定这些聚合，但提供了一些默认值。通常，OpenTelemetry SDK提供常见的聚合（例如：求和、计数、最新值和直方图），它们被可视化工具和遥测back-end支持。

不同于请求的tracing（其目的在于捕获请求的生命周期和提供请求各部分独立的上下文），metrics目的在于提供聚合的统计信息。使用metric的例子包括：

- 报告一个service每种协议类型，读取字节的总数。
- 报告读取字节的总数和每个请求的字节数。
- 报告一个系统调用持续的时间。
- 报告请求的大小，以确定趋势。
- 报告某个进程CPU或内存的使用率。
- 报告一个账户平均余额。
- 报告当前正在被处理的活跃请求数量。

> 更多信息，请参见[metrics specification][metrics specification].

[metrics specification]: /docs/reference/specification/overview/#metric-signal
