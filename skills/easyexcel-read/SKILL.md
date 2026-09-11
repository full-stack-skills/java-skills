---
name: easyexcel-read
description: 基于 Alibaba EasyExcel（com.alibaba:easyexcel:4.0.3）的 Excel 数据读取技能。覆盖监听器模式（ReadListener/AnalysisEventListener/PageReadListener）、同步读取（doReadSync）、多 Sheet 读取、@ExcelProperty(index/name)匹配、多行表头（headRowNumber）、日期数字自定义转换器（@DateTimeFormat/@NumberFormat/Converter）、监听器异常处理（onException + ExcelDataConvertException）、额外信息读取（批注/超链接/合并单元格，extraRead + CellExtra）、公式和单元格类型（CellData<T>）、不创建对象的读（ReadListener<Map<Integer,String>>）、Web 上传读取（MultipartFile→InputStream）。当用户需要解析 Excel（导入数据/批量入库/对账文件/用户上传/Excel 转对象/Excel 转 Map）时使用此技能，不适用于按预制模板填充（改用 easyexcel-fill）、不适用于程序化生成 Excel（改用 easyexcel-write）。
license: Apache-2.0
---

# EasyExcel 数据读取（Read）

> 官方文档：https://easyexcel.opensource.alibaba.com/docs/current/quickstart/read
> 官方示例：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/read/ReadTest.java
> 当前版本：`com.alibaba:easyexcel:4.0.3`（Apache-2.0，仓库已归档，仅做 Bug 修复）

本技能专注于 **"读取 Excel 数据"** 场景。当用户需要解析 `.xlsx`/`.xls` 文件（导入数据、批量入库、对账文件解析、用户上传）时，应当使用本技能。

## Capability Boundaries

### ✅ 强项
1. 监听器模式（流式，大文件不 OOM）
2. 同步读取（小文件一次性返回 List）
3. 多 Sheet 读取（全部 / 部分）
4. 多行表头支持（`headRowNumber`）
5. 自定义转换器（日期/数字/枚举）
6. 异常处理（行级容错）
7. 额外信息读取（批注/超链接/合并单元格）
8. 公式和单元格原始类型
9. 不创建对象的读（直接得 Map）
10. Web 上传文件读取

### ⚠️ 限制
1. 监听器不能被 Spring 管理（必须 new，且字段需要用构造方法传入 Spring Bean）
2. 一个 sheet 不能重复读取（多次读取需重新构造 reader）
3. 03 版多 sheet 一次性传入避免重复解析
4. `headRowNumber` 不指定时会按 `@ExcelProperty#value()` 表头数量推断
5. `doReadSync()` 不推荐大数据量

### ❌ Out of Scope（不该用本技能的场景，请改用其它技能）
1. **按预制模板填充** → **不适用**本技能，使用 `easyexcel-fill`
2. **程序化生成 Excel** → **不适用**本技能，使用 `easyexcel-write`
3. **CSV 文件** → **不适用**本技能，自行使用 commons-csv / OpenCSV（EasyExcel 也支持 CSV，需 `excelType(CSV)`）
4. **Word/PPT** → **不适用**本技能，使用 Apache POI

## Data Privacy

本技能不收集、存储或传输任何用户数据。所有代码示例仅用于本地开发参考。

## When to use this skill

- 用户说"读取 Excel"、"解析 Excel"、"导入 Excel"、"上传 Excel"
- 用户上传 `.xlsx` 文件，要求把数据读为 Java 对象或 Map
- 业务上有"批量入库"、"对账文件解析"、"动态列读取"等需求
- 需要读取批注/超链接/合并单元格等额外信息

## Quick Start

**典型调用方式（4 种写法）**：

```java
// 写法 1：JDK8+ PageReadListener（since 3.0.0-beta1，最简洁）
EasyExcel.read(fileName, DemoData.class, new PageReadListener<DemoData>(dataList -> {
    for (DemoData data : dataList) {
        log.info("读取到数据: {}", JSON.toJSONString(data));
    }
})).sheet().doRead();

// 写法 2：匿名 ReadListener
EasyExcel.read(fileName, DemoData.class, new ReadListener<DemoData>() {
    @Override public void invoke(DemoData data, AnalysisContext context) { /* 每行回调 */ }
    @Override public void doAfterAllAnalysed(AnalysisContext context) { /* 全部完成 */ }
}).sheet().doRead();

// 写法 3：自定义 ReadListener
EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet().doRead();

// 写法 4：ExcelReader（一个文件一个 reader）
try (ExcelReader excelReader = EasyExcel.read(fileName, DemoData.class,
        new DemoDataListener()).build()) {
    ReadSheet readSheet = EasyExcel.readSheet(0).build();
    excelReader.read(readSheet);
}
```

> **官方原文**："PageReadListener 默认每次会读取100条数据 然后返回过来 直接调用使用数据就行"

## Workflow

Step 1. **确认场景** — 数据规模、是否包含额外信息、是否有 DTO
Step 2. **定义数据模型** — 编写 DTO/VO，标注 `@ExcelProperty` 等注解
Step 3. **选择读取方式** — 监听器（流式）/ 同步（小文件）/ 不创建对象
Step 4. **实现 ReadListener** — 注入 DAO（构造方法）；处理 `invoke` / `doAfterAllAnalysed` / `onException` / `invokeHead` / `extra`
Step 5. **执行读取** — `doRead()` 或 `excelReader.read(readSheet)`
Step 6. **验证输出** — 检查行数、转换异常、批注/超链接捕获

## Critical: 模式选择决策

```
数据规模？
├── < 1000 行 + 一次性返回 → 同步模式：doReadSync()
├── < 1 万行 + 简单处理 → PageReadListener
├── > 1 万行 + 入库 → ReadListener + 批量入库（每 2000 行）
└── 不需要对象 + 动态列 → ReadListener<Map<Integer, String>>

需要额外信息？
├── 批注 → extraRead(CellExtraTypeEnum.COMMENT)
├── 超链接 → extraRead(CellExtraTypeEnum.HYPERLINK)
└── 合并单元格 → extraRead(CellExtraTypeEnum.MERGE)

需要多 Sheet？
├── 全部 → doReadAll()
└── 部分 → ExcelReader.read(readSheet1, readSheet2, ...)
```

## Critical: 标准监听器模板

```java
@Slf4j
public class DemoDataListener implements ReadListener<DemoData> {
    /**
     * 官方原文："DemoDataListener 不能被spring管理，要每次读取excel都要new"
     */
    private static final int BATCH_COUNT = 100;
    private List<DemoData> cachedDataList =
        ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
    private DemoDAO demoDAO;

    public DemoDataListener() {
        demoDAO = new DemoDAO();
    }

    /**
     * 官方原文："如果使用了spring,请使用这个构造方法。
     *            每次创建Listener的时候需要把spring管理的类传进来"
     */
    public DemoDataListener(DemoDAO demoDAO) {
        this.demoDAO = demoDAO;
    }

    @Override
    public void invoke(DemoData data, AnalysisContext context) {
        log.info("解析到一条数据:{}", JSON.toJSONString(data));
        cachedDataList.add(data);
        if (cachedDataList.size() >= BATCH_COUNT) {
            saveData();
            cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
        }
    }

    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        saveData();  // ⚠️ 处理最后一批
        log.info("所有数据解析完成！");
    }

    private void saveData() {
        log.info("{}条数据，开始存储数据库！", cachedDataList.size());
        demoDAO.save(cachedDataList);
        log.info("存储数据库成功！");
    }
}

// 持久层（官方原文："如果是mybatis,尽量别直接调用多次insert,
//            自己写一个mapper里面新增一个方法batchInsert"）
public class DemoDAO {
    public void save(List<DemoData> list) {
        // mybatis batchInsert
    }
}
```



## Reference Library（按需加载）

详细代码与注释已拆分到 `references/`。按场景加载：

### API & 基础模式
- [references/api/4-read-modes.md](references/api/4-read-modes.md) — 4 种基础读取模式（最简 / 列下标 / 多 Sheet / 日期数字转换）
- [references/api/key-classes.md](references/api/key-classes.md) — Key Classes / Methods 速查

### 场景化（多行头 / 同步 / 表头 / 额外信息 / 公式 / 异常 / 不创建对象 / Web 上传）
- [references/scenarios/multi-header-sync-header-extra.md](references/scenarios/multi-header-sync-header-extra.md) — 多行头、同步读取、表头数据、额外信息
- [references/scenarios/formula-celltype-exception-noobj.md](references/scenarios/formula-celltype-exception-noobj.md) — 公式 / 单元格类型 / 异常处理 / 不创建对象
- [references/scenarios/web-upload.md](references/scenarios/web-upload.md) — Web 上传读取

### 故障排查
- [references/troubleshooting/gotchas.md](references/troubleshooting/gotchas.md) — 12 条常见错误与正确做法

## References

- **官方文档**：https://easyexcel.opensource.alibaba.com/docs/current/quickstart/read
- **官方示例**：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/read/ReadTest.java
- **Web 示例**：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/web/WebTest.java
- **项目主页**：https://easyexcel.opensource.alibaba.com/
- **GitHub**：https://github.com/alibaba/easyexcel
- **API 参考**：https://easyexcel.opensource.alibaba.com/docs/current/api/
