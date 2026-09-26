> **本库已归档（只读）。** 内容已全部迁入统一内容库 [`lishui`](https://github.com/lishuiorg/lishui)：
> 本站条目现位于 `content/lishui-history/` 与 `content/en/lishui-history/`，来源层并入该库的 `sources/`（全局共享，6 例重复已合并）。
> 本库保留仅供查阅历史，不再更新；校验工作流已随底座变更移除，现行校验在 `lishui` 库内运行。
> 迁移的理由与取舍见《三层结构集中方案》。

# lishui-history · 溧水历史内容库

溧水一方「溧水历史」分站的内容库。这里存放的是**内容**，站点代码在 `site-lishi`，共享底座在 `lishui-kit`，部署到 `lishi.lishui.org`。

分站计划见《溧水历史分站计划 v1.0》；总体架构、内容模型、双语规则与许可以《溧水一方建设规划 v1.3》为准。本库与框架无关：站点用什么生成器都读得动，内容一行都不用改。

## 两层结构

| 目录 | 放什么 |
| --- | --- |
| `sources/` | **来源层。** 每条外部资料的著录卡：`fulltext/` 公有领域旧志全文，`excerpts/` 受版权保护资料的摘录卡，`records/` 政府页面与文保名录的链接档案。字段与硬规则见 `sources/rights.md` |
| `content/` | **成果层。** 本站自撰的条目，中文稿在 `events/`、`places/`、`articles/` 下，英文稿在 `en/` 下的对称路径 |

成果层文字采用 [CC BY 4.0](LICENSE) 授权。来源层各条目的授权状态见其 `rights` 字段，不随本库授权一并转移。

## 内容模型

三类实体，取值表在 `schema/enums.json`：

- `event` 事件——必须有 `time`（含 `start` 与 `precision`）与 `outcome`
- `place` 古迹——必须有 `place_type` 与 `era`；填 `protection_level` 就必须带 `protection_batch`
- `article` 文章——必须有 `genre`（沿革 / 考据 / 摘录 / 综述）

人物不单独建条，只在 `related` 里引用人物分站的条目 ID。

`content/` 与 `content/en/` 下的条目**共用同一个 ID**（靠 `lang` 区分语言），英文稿放在对称路径上。`published` 条目必须中英成对，缺任一份则两份都不得发布。

## 校验

```bash
npm install                        # 首次；lishui-kit 以 file: 依赖装在 node_modules 下
node scripts/validate.mjs          # 输出全部问题
node scripts/validate.mjs --json   # 输出 JSON，供 CI 使用
```

校验引擎在共享底座 `lishui-kit/validate/engine.mjs`，本库只写历史分站特有的规则。引擎本身零依赖，但本库要能解析到 `lishui-kit`，所以仍需 `npm install` 一次。

**通用六项**（引擎提供，全站群一致）：

1. **来源层**——id 与文件名一致、授权状态入表、链接类来源必须有访问日期、出版者为中文时必须补 `publisherEn`；
2. **成果层**——必填字段、schema 之外的字段、ID 与路径一致、取值入表、来源 `ref` 可解析、`related` 可解析；
3. **英文稿**——展示字段不得残留中文，纪年必须能查到译法；
4. **词表覆盖**——取值表里的中文取值必须都能翻成英文；
5. **双语配对**——`published` 条目缺英文稿即阻断；
6. **专名一致性**——英文稿标题中的专名必须与 `glossary.csv` 一致。

**历史四项附加**（本库 `scripts/validate.mjs` 以 `extra` 回调注入）：

1. `event` 必须有完整的 `time` 与 `outcome`，`place_ref` 必须指向存在的地点条目；
2. `place` 必须有 `place_type` 与 `era`，填了 `protection_level` 就必须带 `protection_batch`，坐标须落在溧水境内；
3. `article` 必须有 `genre`，引文原文只能取自 `rights` 为 `public-domain` 的来源；
4. 年代合理性——落在置县（591）至今区间之外的给警告，正文须说明依据。

推送到 `main` 或提交 PR 时由 `.github/workflows/validate.yml` 自动执行。

## 写一条新条目的顺序

1. 在 `sources/` 建或复用来源卡，填好 `rights`、版本与 `locator_hint`；
2. 定年份与精度，旧志纪年换算成公元年份，换算不确定时把 `precision` 降一档并在正文写明依据；
3. 正文按「起因 → 时间地点 → 过程 → 结果与影响 → 存疑之处」写；
4. 把相关地点、事件、文章的 ID 填进 `related`；
5. 写英文稿，年号、朝代、职官、旧志引文按 `glossary.csv` 的规则处理；
6. 定 `depth`，状态置 `review`，提交 PR；校验通过后改 `published`。

详见 `CONTRIBUTING.md`。
