# 4 种基础读取模式

> 摘自 SKILL.md 详细版。完整代码示例 + 注释。

## 模式 1：最简读取（4 种写法）

```java
// DTO
@Getter
@Setter
@EqualsAndHashCode
public class DemoData {
    private String string;
    private Date date;
    private Double doubleData;
}

@Test
public void simpleRead() {
    String fileName = "demo.xlsx";

    // 写法 1：JDK8+ PageReadListener
    EasyExcel.read(fileName, DemoData.class, new PageReadListener<DemoData>(dataList -> {
        for (DemoData d : dataList) {
            log.info("读取到一条数据{}", JSON.toJSONString(d));
        }
    })).sheet().doRead();

    // 写法 2：匿名 ReadListener
    EasyExcel.read(fileName, DemoData.class, new ReadListener<DemoData>() {
        private static final int BATCH_COUNT = 100;
        private List<DemoData> cachedDataList =
            ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);

        @Override
        public void invoke(DemoData data, AnalysisContext context) {
            cachedDataList.add(data);
            if (cachedDataList.size() >= BATCH_COUNT) {
                saveData();
                cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
            }
        }
        @Override
        public void doAfterAllAnalysed(AnalysisContext context) { saveData(); }

        private void saveData() {
            log.info("{}条数据，开始存储数据库！", cachedDataList.size());
        }
    }).sheet().doRead();

    // 写法 3：自定义 Listener
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet().doRead();

    // 写法 4：ExcelReader
    try (ExcelReader excelReader = EasyExcel.read(fileName, DemoData.class,
            new DemoDataListener()).build()) {
        ReadSheet readSheet = EasyExcel.readSheet(0).build();
        excelReader.read(readSheet);
    }
}
```

## 模式 2：指定列的下标或列名

```java
@Getter
@Setter
@EqualsAndHashCode
public class IndexOrNameData {
    @ExcelProperty(index = 2)
    private Double doubleData;
    @ExcelProperty("字符串标题")
    private String string;
    @ExcelProperty("日期标题")
    private Date date;
}

@Test
public void indexOrNameRead() {
    EasyExcel.read(fileName, IndexOrNameData.class, new IndexOrNameDataListener())
        .sheet().doRead();
}
```

> **官方原文**："不建议 index 和 name 同时用，要么只用index，要么只用name匹配"

## 模式 3：多 Sheet 读取

```java
@Test
public void repeatedRead() {
    String fileName = "demo.xlsx";

    // 读取全部 sheet
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).doReadAll();

    // 读取部分 sheet
    try (ExcelReader excelReader = EasyExcel.read(fileName).build()) {
        ReadSheet readSheet1 = EasyExcel.readSheet(0).head(DemoData.class)
                .registerReadListener(new DemoDataListener()).build();
        ReadSheet readSheet2 = EasyExcel.readSheet(1).head(DemoData.class)
                .registerReadListener(new DemoDataListener()).build();
        // ⚠️ 必须把 sheet1 sheet2 一起传进去，不然03版excel会读取多次浪费性能
        excelReader.read(readSheet1, readSheet2);
    }
}
```

> **官方原文**："一个sheet不能读取多次，多次读取需要重新读取文件"
> **官方原文**："必须把sheet1 sheet2 一起传进去，不然03版excel会读取多次浪费性能"

## 模式 4：日期/数字/自定义格式转换

```java
@Getter
@Setter
@EqualsAndHashCode
public class ConverterData {
    @ExcelProperty(converter = CustomStringStringConverter.class)
    private String string;
    @DateTimeFormat("yyyy年MM月dd日HH时mm分ss秒")
    private String date;
    @NumberFormat("#.##%")
    private String doubleData;
}

public class CustomStringStringConverter implements Converter<String> {
    @Override
    public Class<?> supportJavaTypeKey() { return String.class; }

    @Override
    public CellDataTypeEnum supportExcelTypeKey() { return CellDataTypeEnum.STRING; }

    @Override
    public String convertToJavaData(ReadConverterContext<?> context) {
        return "自定义：" + context.getReadCellData().getStringValue();
    }

    @Override
    public WriteCellData<?> convertToExcelData(WriteConverterContext<String> context) {
        return new WriteCellData<>(context.getValue());
    }
}

@Test
public void converterRead() {
    EasyExcel.read(fileName, ConverterData.class, new ConverterDataListener())
        // .registerConverter(new CustomStringStringConverter())  // 全局注册
        .sheet().doRead();
}
```

> **官方原文**："registerConverter 会变成全局，所有java为string,excel为string都会用这个转换器；如果就想单个字段使用请使用@ExcelProperty 指定converter"
