# 高级特性

## 动态表头

```java
@Test
public void dynamicHeadWrite() {
    EasyExcel.write(fileName)
        .head(head())  // 运行时构造表头
        .sheet("模板")
        .doWrite(data());
}

private List<List<String>> head() {
    List<List<String>> list = new ArrayList<>();
    List<String> head0 = new ArrayList<>();
    head0.add("字符串" + System.currentTimeMillis());
    list.add(head0);
    return list;
}
```

## 自动列宽（不太精确）

```java
EasyExcel.write(fileName, LongestMatchColumnWidthData.class)
    .registerWriteHandler(new LongestMatchColumnWidthStyleStrategy())
    .sheet("模板").doWrite(dataLong());
```

## 自定义拦截器：单元格下拉框

```java
@Slf4j
public class CustomSheetWriteHandler implements SheetWriteHandler {
    @Override
    public void afterSheetCreate(SheetWriteHandlerContext context) {
        CellRangeAddressList cellRangeAddressList = new CellRangeAddressList(1, 2, 0, 0);
        DataValidationHelper helper = context.getWriteSheetHolder().getSheet()
            .getDataValidationHelper();
        DataValidationConstraint constraint =
            helper.createExplicitListConstraint(new String[]{"测试1", "测试2"});
        DataValidation dataValidation =
            helper.createValidation(constraint, cellRangeAddressList);
        context.getWriteSheetHolder().getSheet().addValidationData(dataValidation);
    }
}
```

## 自定义拦截器：超链接

```java
@Slf4j
public class CustomCellWriteHandler implements CellWriteHandler {
    @Override
    public void afterCellDispose(CellWriteHandlerContext context) {
        Cell cell = context.getCell();
        if (BooleanUtils.isTrue(context.getHead()) && cell.getColumnIndex() == 0) {
            CreationHelper createHelper = context.getWriteSheetHolder()
                .getSheet().getWorkbook().getCreationHelper();
            Hyperlink hyperlink = createHelper.createHyperlink(HyperlinkType.URL);
            hyperlink.setAddress("https://github.com/alibaba/easyexcel");
            cell.setHyperlink(hyperlink);
        }
    }
}
```

## 03 版兼容

```java
EasyExcel.write(fileName, DemoData.class)
    .excelType(ExcelTypeEnum.XLS)  // 默认 XLSX
    .sheet("模板").doWrite(data());
```
