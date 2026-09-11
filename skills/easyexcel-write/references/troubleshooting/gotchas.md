# Gotchas（常见错误）

| # | 错误 | 正确做法 |
|---|------|---------|
| 1 | `@ExcelProperty` index 和 value 同时用 | 只用一种（index 或 name） |
| 2 | 一次性写入 10 万行 | 改为分批 `excelWriter.write()` |
| 3 | 每次写入都 new `WriteCellStyle` | 缓存 style 对象，最多 6W 个 |
| 4 | 用 `WriteCellData` 没设 `setType` | 设 `setType(CellDataTypeEnum.STRING/...)` |
| 5 | 批注没设 `inMemory(true)` | 批注必须 `inMemory(true)` |
| 6 | Web 导出先写文件再读入 response | 直接 `EasyExcel.write(response.getOutputStream())` |
| 7 | 没关闭 `ExcelWriter` 写入失败 | try-with-resources 自动 `finish()` |
| 8 | 导出 CSV | EasyExcel 也支持，但中文编码注意 `Charset` |
| 9 | 大图片全存内存 | 上传 OSS 后用 URL |
| 10 | `autoCloseStream(true)` 在失败回 JSON 场景 | 设 `autoCloseStream(false)` |
| 11 | 跨 sheet 顺序不对 | sheetNo 从 0 开始 |
| 12 | 用 `Sheet` 写自定义逻辑时未注册拦截器 | 写 `WriteHandler` 后用 `registerWriteHandler()` |
