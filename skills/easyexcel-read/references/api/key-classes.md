# Key Classes / Methods

| 类型 | 名称 | 用途 |
|------|------|------|
| 入口 | `EasyExcel.read(path, head, listener)` | 读文件 + DTO + 监听器 |
| 入口 | `EasyExcel.read(inputStream, head, listener)` | 读流（Web 上传） |
| 入口 | `EasyExcel.read(path)` | 不指定 head（动态） |
| 入口 | `EasyExcel.read(path, head).sheet().doReadSync()` | 同步读取 |
| 核心 | `ExcelReader` | 多次读取容器 |
| 核心 | `ReadSheet` | 单个 sheet |
| 核心 | `ReadListener<T>` | 监听器接口 |
| 核心 | `AnalysisEventListener<T>` | 监听器基类（带 head/extra） |
| 核心 | `PageReadListener<T>` | JDK8+ 简化监听器（since 3.0.0-beta1） |
| 核心 | `AnalysisContext` | 读取上下文（含 sheetNo/rowIndex） |
| 注解 | `@ExcelProperty` | 表头/索引/转换器 |
| 注解 | `@DateTimeFormat` `@NumberFormat` | 格式 |
| 注解 | `@ExcelIgnore` `@ExcelIgnoreUnannotated` | 忽略字段 |
| 配置 | `headRowNumber(N)` | 表头行数 |
| 配置 | `extraRead(CellExtraTypeEnum.X)` | 额外信息读取 |
| 配置 | `registerConverter(converter)` | 全局转换器 |
| 异常 | `ExcelDataConvertException` | 转换异常（行/列号） |
| 数据 | `CellData<T>` | 单元格原始类型（含公式） |
| 数据 | `CellExtra` | 额外信息（批注/超链接/合并） |
| 工具 | `EasyExcel.readSheet(0).build()` | 创建 ReadSheet |
| 工具 | `EasyExcel.readSheet("name").build()` | 按名称 |
