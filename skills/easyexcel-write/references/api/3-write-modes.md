# 3 种基础写入模式

> 摘自 SKILL.md 详细版。

## 模式 1：最简写入（推荐）

```java
// DTO
@Getter
@Setter
@EqualsAndHashCode
public class DemoData {
    @ExcelProperty("字符串标题")
    private String string;
    @ExcelProperty("日期标题")
    private Date date;
    @ExcelProperty("数字标题")
    private Double doubleData;
    @ExcelIgnore
    private String ignore;  // 不导出
}

// 写入
EasyExcel.write(fileName, DemoData.class)
    .sheet("模板")
    .doWrite(data());
```

## 模式 2：选择性导出列

```java
// 排除某列
Set<String> exclude = new HashSet<>();
exclude.add("date");
EasyExcel.write(fileName, DemoData.class)
    .excludeColumnFiledNames(exclude)
    .sheet("模板").doWrite(data());

// 只导出某列
Set<String> include = new HashSet<>();
include.add("date");
EasyExcel.write(fileName, DemoData.class)
    .includeColumnFiledNames(include)
    .sheet("模板").doWrite(data());
```

## 模式 3：多次写入（同一 Sheet / 不同 Sheet / 不同对象）

```java
try (ExcelWriter excelWriter = EasyExcel.write(fileName, DemoData.class).build()) {
    // 同一 sheet 多次写入
    WriteSheet writeSheet = EasyExcel.writerSheet("模板").build();
    for (int i = 0; i < 5; i++) {
        excelWriter.write(data(), writeSheet);
    }

    // 不同 sheet 同一对象
    for (int i = 0; i < 5; i++) {
        WriteSheet sheet = EasyExcel.writerSheet(i, "模板" + i).build();
        excelWriter.write(data(), sheet);
    }
}
```
