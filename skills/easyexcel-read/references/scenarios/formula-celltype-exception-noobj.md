# 公式 / 单元格类型 / 异常处理 / 不创建对象

> 摘自 SKILL.md 详细版。涵盖 4 个进阶读取场景。

## 读取公式和单元格类型

```java
@Getter
@Setter
@EqualsAndHashCode
public class CellDataReadDemoData {
    private CellData<String> string;
    private CellData<Date> date;        // excel 中是 number，包装后是 Date
    private CellData<Double> doubleData;
    private CellData<String> formulaValue;
}

@Test
public void cellDataRead() {
    EasyExcel.read(fileName, CellDataReadDemoData.class, new CellDataDemoHeadDataListener())
        .sheet().doRead();
}
```

> **官方原文**："依赖性公式可能读不到，后续会修复"

## 数据转换异常处理

```java
@Override
public void onException(Exception exception, AnalysisContext context) {
    log.error("解析失败，但是继续解析下一行:{}", exception.getMessage());
    // 抛出异常则停止读取；不抛出则继续读取下一行
    if (exception instanceof ExcelDataConvertException) {
        ExcelDataConvertException ex = (ExcelDataConvertException) exception;
        log.error("第{}行，第{}列解析异常，数据为:{}",
            ex.getRowIndex(), ex.getColumnIndex(), ex.getCellData());
    }
}

@Test
public void exceptionRead() {
    EasyExcel.read(fileName, ExceptionDemoData.class, new DemoExceptionListener())
        .sheet().doRead();
}
```

## 不创建对象的读

```java
@Slf4j
public class NoModelDataListener extends AnalysisEventListener<Map<Integer, String>> {
    private static final int BATCH_COUNT = 5;
    private List<Map<Integer, String>> cachedDataList =
        ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);

    @Override
    public void invoke(Map<Integer, String> data, AnalysisContext context) {
        log.info("解析到一条数据:{}", JSON.toJSONString(data));
        cachedDataList.add(data);
        if (cachedDataList.size() >= BATCH_COUNT) {
            saveData();
            cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
        }
    }

    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        saveData();
    }

    private void saveData() {
        log.info("{}条数据，开始存储数据库！", cachedDataList.size());
    }
}

@Test
public void noModelRead() {
    EasyExcel.read(fileName, new NoModelDataListener()).sheet().doRead();
}
```

> **官方原文**："不创建对象的读"用于动态列、未知表头结构
