---
title: "Distributions"
description: >-
 distribution是一个OpenTelemetry组件的自定义版本，不要和fork弄混了。
weight: 90
---
OpenTelemetry项目由多个[组件](../components)构成，这些组件支持多个[signals](../signals)。OpenTelemetry的参考实现，如下：

- [特定开发语言的监测库](../instrumenting)
- [Collector二进制包](../data-collection)

From any reference implementation a distribution may be created.

## 什么是distribution?

distribution是OpenTelemetry的一个自定义版本, 不要和fork弄混了。distribution是对上游包含一些自定义内容的OpenTelemetry仓库的包装。自定义内容包括：

- 为特定back-end或厂商，简化使用或自定义的脚本
- 更改back-end，厂商或用户所需的默认参数
- 额外的包选项，这些也许是厂商或用户所特有的
- 超出OpenTelemetry的测试，性能和安全
- 超出OpenTelemetry的额外能力
- 不能从OpenTelemetry获得的功能

Distributions可以分为如下类别：

- **"Pure":** 这些distributions和上游有相同功能100%的兼容。自定义内容通常是为了简化打包或使用。这些也许是back-end，厂商或用户所特有的。
- **"Plus":** 这些distributions是上游相同功能的增强。自定义内容包括除正常功能外一下额外的功能。例如，这些OpenTelemetry项目不包含的指标监测库或商业exporter。
- **"Minus":** 这些distributions是上游功能的精简。例如，移除一些OpenTelemetry项目中的指标检测库，receivers、processors，exporters，extensions。这些distributions也许提供增强的技术支持和安全特性。

## 谁可以创建发行版?

任何人都可以创建发行版。今天，已经有数个[厂商](../../../vendors)提供发行版。另外，如果最终用户希望使用[Registry](../../../registry)上的非OpenTelemetry项目的组件，也可以自己创建发行版。 

## Contribution or distribution?

在继续读下去和学习如何创建发行版之前，先问自己一个问题，在OpenTelemetry组件基础上增加你的工作，对大家有什么收益？建议从如下几个方面考虑：

- 你的"简化使用"的脚本能被推广吗？
- 改变默认设置对所有人都是更好的选项吗？
- 额外的包选项真的很具体吗？
- 也许你的测试，性能和安全已经覆盖了参考实现了吗？
- 如果你的功能作为标准的一部分，已经和社区做过检查了吗？

## 创建你的发行版

### Collector

下面这个blog是介绍如何创建Collector发行版：
[&#34;Building your own OpenTelemetry Collector distribution&#34;](https://medium.com/p/42337e994b63)

如果你正在构建发行版，[OpenTelemetry Collector Builder](https://github.com/open-telemetry/opentelemetry-collector/tree/main/cmd/builder) 也许是一个很好的开始。

### 特定开发语言的指标监测库

自定义特定开发语言的指标监测库，扩展机制可以参考：

- [Javaagent](../../instrumentation/java/extensions)

## 关于发行版你需要知道的事

当使用OpenTelemetry项目的标识，如logo和名字，作为你的发行版，请确保符合OpenTelemetry Marketing的要求。

当前，OpenTelemetry项目没有认证的发行版。将来，也许会认证发行版和合作者，与kubernetes项目类似。在评估发行版的时候，确保不要与厂商绑死。

> 对发行版的所有支持来自发行版的作者，而非OpenTelemetry。

[guidelines]: https://github.com/open-telemetry/community/blob/main/marketing-guidelines.md
