# 多行头 / 同步读取 / 表头数据 / 额外信息

> 摘自 SKILL.md 详细版。涵盖 4 个常被组合使用的场景。

## 多行头

```java
@Test
public void complexHeaderRead() {
    // 模板默认 headRowNumber=1，多行头用 headRowNumber(N)
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener())
        .sheet()
        .headRowNumber(2)  // 2 行表头
        .doRead();
}
```

> **官方原文**："headRowNumber不指定时，会根据传入class的@ExcelProperty#value()的表头数量决定行数；不传入class则默认为1；指定了headRowNumber则以此为准"

## 同步读取（小文件）

```java
@Test
public void synchronousRead() {
    String fileName = "demo.xlsx";

    // 指定 class 返回 List
    List<DemoData> list = EasyExcel.read(fileName).head(DemoData.class)
        .sheet().doReadSync();
    for (DemoData data : list) {
        LOGGER.info("读取到数据:{}", JSON.toJSONString(data));
    }

    // 不指定 class 返回 List<Map<Integer, String>>
    List<Map<Integer, String>> listMap = EasyExcel.read(fileName)
        .sheet().doReadSync();
    for (Map<Integer, String> data : listMap) {
        LOGGER.info("读取到数据:{}", JSON.toJSONString(data));
    }
}
```

> **官方原文**："不推荐使用，如果数据量大会把数据放到内存里面"

## 读取表头数据

```java
@Slf4j
public class DemoHeadDataListener extends AnalysisEventListener<DemoData> {
    @Override
    public void invokeHead(Map<Integer, ReadCellData<?>> headMap, AnalysisContext context) {
        log.info("解析到一条头数据:{}", JSON.toJSONString(headMap));
        // 转成 Map<Integer,String>：
        // 方案 1：不implements ReadListener 而是 extends AnalysisEventListener
        // 方案 2：调用 ConverterUtils.convertToStringMap(headMap, context) 自动转换
    }

    @Override public void invoke(DemoData data, AnalysisContext context) {}
    @Override public void doAfterAllAnalysed(AnalysisContext context) {}
}

@Test
public void headerRead() {
    EasyExcel.read(fileName, DemoData.class, new DemoHeadDataListener()).sheet().doRead();
}
```

## 额外信息（批注/超链接/合并单元格）

```java
@Slf4j
public class DemoExtraListener implements ReadListener<DemoExtraData> {
    @Override public void invoke(DemoExtraData data, AnalysisContext context) {}
    @Override public void doAfterAllAnalysed(AnalysisContext context) {}

    @Override
    public void extra(CellExtra extra, AnalysisContext context) {
        log.info("读取到了一条额外信息:{}", JSON.toJSONString(extra));
        switch (extra.getType()) {
            case COMMENT:
                log.info("额外信息是批注,在rowIndex:{},columnIndex;{},内容是:{}",
                    extra.getRowIndex(), extra.getColumnIndex(), extra.getText());
                break;
            case HYPERLINK:
                log.info("额外信息是超链接,在rowIndex:{},columnIndex;{},内容是:{}",
                    extra.getRowIndex(), extra.getColumnIndex(), extra.getText());
                break;
            case MERGE:
                log.info("额外信息是超链接,覆盖了一个区间,在firstRowIndex:{},firstColumnIndex;{}," +
                    "lastRowIndex:{},lastColumnIndex:{}",
                    extra.getFirstRowIndex(), extra.getFirstColumnIndex(),
                    extra.getLastRowIndex(), extra.getLastColumnIndex());
                break;
        }
    }
}

@Test
public void extraRead() {
    EasyExcel.read(fileName, DemoExtraData.class, new DemoExtraListener())
        .extraRead(CellExtraTypeEnum.COMMENT)     // 批注 默认不读取
        .extraRead(CellExtraTypeEnum.HYPERLINK)  // 超链接 默认不读取
        .extraRead(CellExtraTypeEnum.MERGE)      // 合并单元格 默认不读取
        .sheet().doRead();
}
```

> **官方原文**："批注、超链接、合并单元格信息默认不读取，需显式调用"
