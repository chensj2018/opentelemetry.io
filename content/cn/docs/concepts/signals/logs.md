---
title: "Logs"
description: >-
  log是带有时间戳的文本格式记录，既可以是结构化的（推荐）也可以是非结构化的，并带有元数据。 
weight: 3
---
**log** 是带有时间戳的文本格式记录，既可以是结构化的（推荐）也可以是非结构化，并带有元数据。同时log有独立的数据源，也可以附着在span上。在OpenTelemetry中，任何不是分布式trace或metric的数据都是log。例如，*event*是log的一种类型。log经常被用于确定造成问题的根本原因，并且通常包含谁发生了改变和改变的结果是什么等信息。

> 更多的信息，请参阅[logs规范][logs specification].

[logs specification]: /docs/reference/specification/overview/#log-signal
