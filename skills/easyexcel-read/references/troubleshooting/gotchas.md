# Gotchas（常见错误）

| # | 错误 | 正确做法 |
|---|------|---------|
| 1 | 监听器标 `@Component` / `@Service` | 监听器**不能**被 Spring 管理，必须 `new` |
| 2 | 监听器直接 `@Autowired` DAO | 通过**构造方法**注入 Spring Bean |
| 3 | 同一 `ExcelReader` 多次 `read()` 同一 sheet | 一个 sheet 不能重复读取，需重新 build |
| 4 | 多 sheet 逐个 `read()` | 必须 `excelReader.read(sheet1, sheet2, ...)` 一次性传入 |
| 5 | 大文件用 `doReadSync()` | 用 `ReadListener` 流式 |
| 6 | `onException` 直接抛出 | 不抛出则继续读，抛出则停止 |
| 7 | 全局 `registerConverter` 后字段都被影响 | 单字段用 `@ExcelProperty(converter=...)` |
| 8 | `extraRead` 不调用就以为能读到批注 | 批注/超链接/合并默认不读，需显式调用 |
| 9 | `headRowNumber` 与表头实际行数不匹配 | 多级表头必须正确指定 |
| 10 | Web 上传时 `MultipartFile.getInputStream()` 没关闭 | 用 try-with-resources 包裹 |
| 11 | 03 版多 sheet 浪费性能 | 一次性传入 `excelReader.read(sheet1, sheet2, ...)` |
| 12 | 同步读取用在大数据量 | 改用监听器 |
