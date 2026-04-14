---
tags:
  - time
  - timezone
  - computing
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# GMT、UTC 与时区概念整理

## Summary

这页用来区分 `naive / aware datetime`、`GMT`、`UTC`、时区、夏令时和 `Locale` 这些容易混在一起的概念。

## 单纯型（naive）本地化和时区意识型（aware）

当使用 `datetime.now()` 得到一个 `datetime` 对象的时候，此时该对象没有任何关于时区的信息，即 `tzinfo` 属性为 `None`。这种对象被称为 **naive datetime object**。

相对的，aware datetime object 指的是存储了时区信息的 datetime object。

```python
from datetime import datetime
native = datetime.now()
native.tzinfo()

# 输出：None
```

```python
import pytz
aware = datetime.now(pytz.utc)
aware
# 输出： datetime.datetime(2023, 5, 13, 12, 24, 26, 500566, tzinfo=<UTC>)
```

## GMT

格林威治皇家天文台为了海上霸权的扩张计划，在十七世纪就开始进行天体观测。为了天文观测，选择了穿过英国伦敦格林威治天文台子午仪中心的一条经线作为零度参考线，这条线简称格林威治子午线。

1884 年在美国华盛顿召开的国际子午线会议，将格林威治子午线设定为本初子午线，并将格林威治平时（GMT, Greenwich Mean Time）作为世界时间标准。由此也确定了全球 24 小时自然时区的划分，所有时区都以和 GMT 之间的偏移量作为参考。

1972 年之前，GMT 一直是世界时间标准。1972 年之后，GMT 不再是正式时间标准。

## UTC

UTC（Coordinated Universal Time），协调世界时，又称世界统一时间、世界标准时间、国际协调时间。

UTC 是现在全球通用的时间标准。UTC 比 GMT 更精准，以原子时计时，适应现代社会的精确计时。

## 本地时间与时区

当我们说当前时刻是某地上午 8:15 的时候，我们说的实际上是本地时间。不同的时区，在同一时刻，本地时间是不同的。

从格林威治本初子午线起，经度每向东或者向西间隔 15°，就划分一个时区。在这个区域内，大家使用同样的标准时间。

时区常见表示方式：

- `GMT+08:00`
- `UTC+08:00`

在开发中通常可以把 `GMT` 和 `UTC` 近似视为等价，但更准确的全球标准是 `UTC`。

## 夏令时

DST（Daylight Saving Time），夏令时又称夏季时间，或者夏时制。它是为节约能源而人为规定地方时间的制度。一般在天亮早的夏季人为将时间提前一小时，以更充分利用光照资源。

## 本地化

在计算机中，通常使用 `Locale` 表示一个国家或地区的日期、时间、数字、货币等格式。`Locale` 由 `语言_国家` 的字母缩写构成，例如：

- `zh_CN`：中文 + 中国
- `en_US`：英文 + 美国

对于日期来说，不同的 Locale 表示方式不同：

- `zh_CN`：2016-11-30
- `en_US`：11/30/2016

```python
import locale
locale.getdefaultlocale()

# 输出：(en_US, 'UTF-8')
```

## Related

- 暂无内部关联页

## Open Questions

- 哪些业务场景应该强制使用 aware datetime，而不是让应用层自己约定本地时间？
