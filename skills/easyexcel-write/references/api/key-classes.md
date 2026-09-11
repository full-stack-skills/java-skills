# Key Classes / Methods

| 类型 | 名称 | 用途 |
|------|------|------|
| 入口 | `EasyExcel.write(path, head)` | 写文件 + 表头类 |
| 入口 | `EasyExcel.write(outputStream, head)` | 写流 + 表头类 |
| 入口 | `EasyExcel.write(path)` | 写文件（无表头类，运行时表头） |
| 核心 | `ExcelWriter` | 多次写入容器 |
| 核心 | `WriteSheet` | 单个 sheet |
| 核心 | `WriteTable` | sheet 内的多表 |
| 注解 | `@ExcelProperty` | 表头/索引/转换器 |
| 注解 | `@ExcelIgnore` | 忽略字段 |
| 注解 | `@DateTimeFormat` `@NumberFormat` | 格式 |
| 注解 | `@ColumnWidth` `@HeadRowHeight` `@ContentRowHeight` | 尺寸 |
| 注解 | `@HeadStyle` `@ContentStyle` | 样式 |
| 注解 | `@ContentLoopMerge` | 合并 |
| 配置 | `excludeColumnFiledNames(Set)` | 排除列 |
| 配置 | `includeColumnFiledNames(Set)` | 包含列 |
| 配置 | `inMemory(true)` | 内存模式（批注必须） |
| 配置 | `autoCloseStream(false)` | 不自动关流（失败回 JSON） |
| 配置 | `excelType(ExcelTypeEnum.XLS)` | 03 版兼容 |
| 配置 | `registerWriteHandler(handler)` | 注册拦截器 |
| 工具 | `EasyExcel.writerSheet(0, "name").build()` | 创建 sheet |
| 工具 | `EasyExcel.writerTable(0).needHead(true).build()` | 创建 table |
