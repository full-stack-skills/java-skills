# 合并单元格

## 注解方式

```java
@ContentLoopMerge(eachRow = 2)  // 每 2 行合并
@ExcelProperty("字符串标题")
private String string;
```

## 策略方式

```java
LoopMergeStrategy loopMergeStrategy = new LoopMergeStrategy(2, 0);
EasyExcel.write(fileName, DemoData.class)
    .registerWriteHandler(loopMergeStrategy)
    .sheet("模板").doWrite(data());
```
