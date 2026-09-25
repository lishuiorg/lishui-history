# 来源层说明

`sources/` 存放的是**来源**，不是内容。这里的每一份文件都只做一件事：把一条外部资料著录清楚，让成果层的条目能挂上一个可追溯、可复核、可判定授权的引用目标。

## 三层目录

| 目录 | 放什么 | 对应 `archive` 取值 |
| --- | --- | --- |
| `fulltext/` | 公有领域旧志的全文或影印本归档。本分站主要是历代《溧水县志》 | `fulltext` / `link-registered` / `catalogued-only` |
| `excerpts/` | 受版权保护资料的摘录卡：只记必要片段与出处，不整篇转录 | `excerpt` |
| `records/` | 政府页面、文保名录、地名录的链接档案。只记链接、访问日期与关键表述 | `link` |

## 来源记录的字段

每份来源是一张 `.md` 卡片，front-matter 字段如下：

```yaml
id: src:shunzhi-lishuixianzhi      # 唯一标识，格式 src:<短名>，全局不重复
type: gazetteer                     # 取值见 schema/enums.json 的 sourceType
title: 顺治《溧水县志》              # 来源题名，中英双语标题用 title / titleEn
titleEn: Lishui County Gazetteer (Shunzhi edition)
rights: public-domain               # public-domain / gov-open / excerpt-only / link-only / permission-required
archive: link-registered            # fulltext / link-registered / catalogued-only / excerpt / link
publisher: 清顺治十五年（1658）刻本   # 出版者、版本或发布机构
url: https://...                    # 可访问地址；无在线版本的写 null 并在 note 说明
accessed: 2026-09-25                # 访问日期，链接类来源必填
locator_hint: 卷次页码               # 该来源通常怎么标注位置，供条目引用时参照
note: 说明                           # 授权判断依据、存疑之处、归档状态
```

## 硬规则

1. **`rights` 必须填，且必须是 `schema/enums.json` 里的取值之一。** 判不准就填 `permission-required`，宁可不引。
2. **无来源的事实不进 `published`。** 条目引用的每一个 `ref` 都必须在本目录下存在对应卡片。
3. **摘录不越界。** `rights` 为 `excerpt-only` 或 `link-only` 的来源，正文里只能引用事实与必要短句，不得整段转录；旧志原文属公有领域的，引用也需标卷次页码，不整篇搬运。
4. **链接要复核。** 名录、政府页面会改版，`accessed` 字段用于记录核对日期；链接失效时先降级 `confidence`，再找替代来源。
5. **弱来源要显形。** 来源为资料整理页面、论坛、二手转述的，`rights` 照填，但在 `note` 里写明权威性不足，并在条目里相应降低 `confidence`。
