---
title: "Baggage"
description: >-
 Baggage是指在span之间传递上下文信息
weight: 4
---
想象一下，如果希望在trace的每个span中都带有 `customerId`这个属性，这将涉及多个service，然而 `customerId`仅在某个service可用，怎么办？为实现这个目标，可以使用OpenTelemetry Baggage在跨系统地传递这个值。

在OpenTelemetry中，"Baggage"是指在span之间传递上下文信息。将k-v数据存储在trace context中，使得在trace的每个span都能使用这些值。

OpenTelemetry使用[Context Propagation](/docs/concepts/signals/traces/#context-propagation)传递Baggage，每个实现库都要实现propagator，用于解析和生成baggage，不需要使用者再显式的实现它。

![OTel Baggage](/img/otel_baggage.png)

## 为什么需要OTel Baggage?

OpenTelemetry可以跨平台和跨框架使用。baggage使得context在相同位置，有相同的格式和相同的模式。这意味着不管什么语言开发的应用程序，都可以读取、解析、使用它们。这对于构建大型的分布式系统是至关重要的。这样各团队无论使用何种开发语言或框架，都能很好的一起工作。

虽然完全可以使用别的东西实现这个功能（例如，标准化的header或者诸如此类的其它东西），这无疑增加了开发团队的负担，需要为每种框架和开发语言构建辅助工具。当有更高优先级的任务时，这个工作往往被无意识地忽略掉。

## 应该怎么使用OTel Baggage?

OTel Baggage应该用于非敏感数据，也就是说这些数据即使暴露给第三方，也是没有问题的。 

通常情况下，用于保存将来使用的信息。这些信息包括像账号、用户ID、产品ID和源IP等。在调用栈中传递这些信息，允许将其加入到下游service的span中，方便在Observability back-end中检索过滤。

需要注意的是Baggage没有内置完整性检查，所以当使用时必须小心谨慎。

![OTel Baggage](/img/otel_baggage-2.png)

## Baggage != Span attributes

需要注意的一个重要的事是：baggage不是一组[Span Attributes](/docs/concepts/signals/traces/#attributes)。当将一些信息添加到baggage后，它不会自动出现在子系统的span中，你必须显式地取出baggage中的值，然后将其添加到span的Attribute中

```csharp
var accountId = Baggage.GetBaggage("AccountId");
Activity.Current?.SetTag("AccountId", accountId);
```

> 更多信息，请参阅[baggage specification][baggage specification].

[baggage specification]: /docs/reference/specification/overview/#baggage-signal
