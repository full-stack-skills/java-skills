---
name: easyexcel-write
description: 基于 Alibaba EasyExcel（com.alibaba:easyexcel:4.0.3）的 Excel 数据写入技能。覆盖最简写入（3 种写法）、复杂表头（多级）、@ExcelProperty/@ExcelIgnore/@DateTimeFormat/@NumberFormat 注解、includeColumn/excludeColumn 选择列、@ColumnWidth/@ContentRowHeight 样式注解、@HeadStyle/@ContentStyle 自定义样式、合并单元格（@ContentLoopMerge/LoopMergeStrategy）、图片导出（File/InputStream/URL/byte[]）、超链接/批注/公式/富文本（WriteCellData）、动态表头、自动列宽、自定义拦截器（CellWriteHandler/SheetWriteHandler/RowWriteHandler）、分页分批写入、Web 下载直接写 OutputStream、03/07 版兼容。当用户需要程序化生成 Excel（无预制模板）导出订单/用户/财务/库存/报表/对账单/审批单等结构化数据时使用此技能，不适用于按预制模板填充（改用 easyexcel-fill）、不适用于读取 Excel（改用 easyexcel-read）。
license: Apache-2.0
---

# EasyExcel 数据写入（Write）

> 官方文档：https://easyexcel.opensource.alibaba.com/docs/current/quickstart/write
> 官方示例：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/write/WriteTest.java
> 当前版本：`com.alibaba:easyexcel:4.0.3`（Apache-2.0，仓库已归档，仅做 Bug 修复）

本技能专注于 **"程序化生成 Excel"** 场景。当用户没有预制模板、要求用 Java 对象/Map 直接生成结构化 Excel（订单、用户、财务、库存、报表、对账单、审批单）时，应当使用本技能。

## Capability Boundaries

### ✅ 强项
1. 最简写入（JDK8+ 写法、传统写法、Builder 写法）
2. 复杂表头（多级表头 `@ExcelProperty({"主标题", "副标题"})`）
3. 选择性导出列（`includeColumnFiledNames` / `excludeColumnFiledNames`）
4. 注解定义格式（`@ColumnWidth` `@ContentRowHeight` `@DateTimeFormat` `@NumberFormat`）
5. 自定义样式（注解方式 / 拦截器方式 / `HorizontalCellStyleStrategy`）
6. 合并单元格（`@ContentLoopMerge` / `LoopMergeStrategy`）
7. 图片导出（File/InputStream/URL/byte[] 多种来源）
8. 超链接/批注/公式/富文本（`WriteCellData` 体系）
9. 动态表头（运行时决定表头）
10. 多次写入/多 Sheet/多 Table
11. Web 直接输出到 `HttpServletResponse`

### ⚠️ 限制
1. 单 Sheet 建议 < 5000 行一次性写入（数据量大用分批）
2. 样式对象最多创建 6W 个，否则 Excel 打不开
3. 图片会驻内存，超大图建议上传到 OSS
4. 批注需要 `inMemory(true)`
5. 03 版（`.xls`）兼容性需用 `excelType(ExcelTypeEnum.XLS)`

### ❌ Out of Scope（不该用本技能的场景，请改用其它技能）
1. **按预制模板填充** → **不适用**本技能，使用 `easyexcel-fill`
2. **读取 Excel 数据** → **不适用**本技能，使用 `easyexcel-read`
3. **CSV 文件处理** → **不适用**本技能，自行使用 commons-csv / OpenCSV
4. **Word/PPT** → **不适用**本技能，使用 Apache POI / Apache POI-XML

## Data Privacy

本技能不收集、存储或传输任何用户数据。所有代码示例仅用于本地开发参考。

## When to use this skill

- 用户说"导出 Excel"、"生成 Excel"、"下载 Excel"、"导出订单/用户/财务/库存"
- 没有预制模板，需要程序化生成结构化 Excel
- 业务上有"分 Sheet（按月份/状态/类型）"、"分 Table（多个表块）"等需求
- 需要导出图片、超链接、公式、富文本、合并单元格

## Quick Start

**典型调用方式（3 种写法）**：

```java
// 写法 1：JDK8+ 用 Supplier（since 3.0.0-beta1，最简洁）
EasyExcel.write(fileName, DemoData.class)
    .sheet("模板")
    .doWrite(() -> data());

// 写法 2：传统 + 已有 List
List<DemoData> data = data();
EasyExcel.write(fileName, DemoData.class)
    .sheet("模板")
    .doWrite(data);

// 写法 3：Builder（多次写入 / 复杂配置）
try (ExcelWriter excelWriter = EasyExcel.write(fileName, DemoData.class).build()) {
    WriteSheet writeSheet = EasyExcel.writerSheet("模板").build();
    excelWriter.write(data(), writeSheet);
}
```

> **官方原文**："数据量建议 5000 以内"（单 sheet 一次性写入推荐上限）。

## Workflow

Step 1. **定义数据模型** — 编写 DTO/VO，标注 `@ExcelProperty` 等注解
Step 2. **选择写入方式** — 单次 `doWrite()` / 多次 `excelWriter.write()` / 多次 `doWrite()` 循环
Step 3. **配置 Builder** — 包含列选择、表头、样式、注册拦截器等
Step 4. **构造数据** — 准备 `List<DTO>` / `List<List<String>>`
Step 5. **执行写入** — try-with-resources 关闭 `ExcelWriter`
Step 6. **验证输出** — 打开生成的 `.xlsx` 检查表头/样式/格式/合并

## Critical: 注解速查

| 注解 | 用途 | 关键属性 |
|------|------|---------|
| `@ExcelProperty` | 指定列名/索引/转换器 | `value`, `index`, `order`, `converter` |
| `@ExcelIgnore` | 忽略该字段 | —— |
| `@ExcelIgnoreUnannotated` | 类级：未标注字段不参与读写 | —— |
| `@DateTimeFormat` | 日期格式 | `value`, `use1904windowing` |
| `@NumberFormat` | 数字格式 | `value`, `roundingMode` |
| `@ColumnWidth` | 列宽 | `value` |
| `@HeadRowHeight` | 表头行高 | `value` |
| `@ContentRowHeight` | 内容行高 | `value` |
| `@HeadStyle` | 表头样式（POI 风格） | fillPatternType, fillForegroundColor, font 等 |
| `@HeadFontStyle` | 表头字体 | fontHeightInPoints, color, bold 等 |
| `@ContentStyle` | 内容样式 | 同 HeadStyle |
| `@ContentFontStyle` | 内容字体 | 同 HeadFontStyle |
| `@ContentLoopMerge` | 循环合并 | `eachRow` |
| `@OnceAbsoluteMerge` | 一次性绝对合并 | `firstRowIndex` `lastRowIndex` 等 |

> **官方原文**：`order` 默认 `Integer.MAX_VALUE`；优先级：`index` > `order` > `value`。
> **官方原文**："不建议 index 和 name 同时用，要么只用index，要么只用name匹配"



## Reference Library（按需加载）

详细代码与注释已拆分到 `references/`。按场景加载：

### API & 基础模式
- [references/api/3-write-modes.md](references/api/3-write-modes.md) — 3 种基础写入模式（最简 / 选择列 / 多次写入）
- [references/api/key-classes.md](references/api/key-classes.md) — Key Classes / Methods 速查

### 模式化（表头 / 样式 / 合并 / 图片 / 富文本 / Web / 高级 / 大数据）
- [references/patterns/header-format-style.md](references/patterns/header-format-style.md) — 复杂表头 / 自定义样式（注解 + 拦截器）
- [references/scenarios/merge-cell.md](references/scenarios/merge-cell.md) — 合并单元格
- [references/scenarios/image-export.md](references/scenarios/image-export.md) — 图片导出（多来源 + 多图嵌入）
- [references/scenarios/hyperlink-comment-formula-richtext.md](references/scenarios/hyperlink-comment-formula-richtext.md) — 超链接 / 批注 / 公式 / 富文本
- [references/scenarios/web-download.md](references/scenarios/web-download.md) — Web 下载（含失败回 JSON）
- [references/scenarios/advanced.md](references/scenarios/advanced.md) — 动态表头 / 自动列宽 / 自定义拦截器 / 03 版兼容
- [references/scenarios/million-row.md](references/scenarios/million-row.md) — 百万级分批写入

### 故障排查
- [references/troubleshooting/gotchas.md](references/troubleshooting/gotchas.md) — 12 条常见错误与正确做法

## References

- **官方文档**：https://easyexcel.opensource.alibaba.com/docs/current/quickstart/write
- **官方示例**：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/write/WriteTest.java
- **项目主页**：https://easyexcel.opensource.alibaba.com/
- **GitHub**：https://github.com/alibaba/easyexcel
- **API 参考**：https://easyexcel.opensource.alibaba.com/docs/current/api/
