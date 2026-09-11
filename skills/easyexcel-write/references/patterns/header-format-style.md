# 复杂表头与格式 / 自定义样式

> 摘自 SKILL.md 详细版。

## 复杂表头与格式

### 多级表头

```java
@Getter
@Setter
@EqualsAndHashCode
public class ComplexHeadData {
    @ExcelProperty({"主标题", "字符串标题"})
    private String string;
    @ExcelProperty({"主标题", "日期标题"})
    private Date date;
    @ExcelProperty({"主标题", "数字标题"})
    private Double doubleData;
}
```

### 数字/日期格式化

```java
@Getter
@Setter
@EqualsAndHashCode
public class ConverterData {
    @ExcelProperty(value = "字符串标题", converter = CustomStringStringConverter.class)
    private String string;
    @DateTimeFormat("yyyy年MM月dd日HH时mm分ss秒")
    @ExcelProperty("日期标题")
    private Date date;
    @NumberFormat("#.##%")
    @ExcelProperty("数字标题")
    private Double doubleData;
}
```

### 列宽与行高

```java
@Getter
@Setter
@EqualsAndHashCode
@ContentRowHeight(10)
@HeadRowHeight(20)
@ColumnWidth(25)
public class WidthAndHeightData {
    @ExcelProperty("字符串标题")
    private String string;
    @ColumnWidth(50)
    @ExcelProperty("数字标题")
    private Double doubleData;
}
```

## 自定义样式

### 注解方式（since 2.2.0-beta1）

```java
@Data
@HeadStyle(fillPatternType = FillPatternType.SOLID_FOREGROUND, fillForegroundColor = 10)
@HeadFontStyle(fontHeightInPoints = 20)
@ContentStyle(fillPatternType = FillPatternType.SOLID_FOREGROUND, fillForegroundColor = 17)
@ContentFontStyle(fontHeightInPoints = 20)
public class DemoStyleData {
    @HeadStyle(fillPatternType = FillPatternType.SOLID_FOREGROUND, fillForegroundColor = 14)
    @HeadFontStyle(fontHeightInPoints = 30)
    @ExcelProperty("字符串标题")
    private String string;
}
```

### 拦截器方式（最灵活）

```java
WriteCellStyle headStyle = new WriteCellStyle();
headStyle.setFillForegroundColor(IndexedColors.RED.getIndex());
WriteFont headFont = new WriteFont();
headFont.setFontHeightInPoints((short) 20);
headStyle.setWriteFont(headFont);

WriteCellStyle contentStyle = new WriteCellStyle();
contentStyle.setFillPatternType(FillPatternType.SOLID_FOREGROUND);
contentStyle.setFillForegroundColor(IndexedColors.GREEN.getIndex());

HorizontalCellStyleStrategy strategy =
    new HorizontalCellStyleStrategy(headStyle, contentStyle);

EasyExcel.write(fileName, DemoData.class)
    .registerWriteHandler(strategy)
    .sheet("模板").doWrite(data());
```

> **官方提示**："不要一直去创建style 记得缓存起来 最多创建6W个就挂了"
