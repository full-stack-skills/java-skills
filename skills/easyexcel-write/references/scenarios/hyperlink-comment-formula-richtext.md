# 超链接 / 批注 / 公式 / 富文本

```java
@Getter
@Setter
@EqualsAndHashCode
public class WriteCellDemoData {
    private WriteCellData<String> hyperlink;
    private WriteCellData<String> commentData;
    private WriteCellData<String> formulaData;
    private WriteCellData<String> writeCellStyle;
    private WriteCellData<String> richText;
}
```

**超链接示例**：

```java
WriteCellData<String> hyperlink = new WriteCellData<>("官方网站");
HyperlinkData hyperlinkData = new HyperlinkData();
hyperlink.setHyperlinkData(hyperlinkData);
hyperlinkData.setAddress("https://github.com/alibaba/easyexcel");
hyperlinkData.setHyperlinkType(HyperlinkType.URL);
writeCellDemoData.setHyperlink(hyperlink);
```

**公式示例**：

```java
WriteCellData<String> formula = new WriteCellData<>();
FormulaData formulaData = new FormulaData();
formula.setFormulaData(formulaData);
formulaData.setFormulaValue("REPLACE(123456789,1,1,2)");
```

**富文本示例**：

```java
WriteCellData<String> richTest = new WriteCellData<>();
richTest.setType(CellDataTypeEnum.RICH_TEXT_STRING);
RichTextStringData richTextStringData = new RichTextStringData();
richTest.setRichTextStringDataValue(richTextStringData);
richTextStringData.setTextString("红色绿色默认");
WriteFont writeFont = new WriteFont();
writeFont.setColor(IndexedColors.RED.getIndex());
richTextStringData.applyFont(0, 2, writeFont);
writeFont = new WriteFont();
writeFont.setColor(IndexedColors.GREEN.getIndex());
richTextStringData.applyFont(2, 4, writeFont);
```

> **官方提示**："inMemory 要设置为true，才能支持批注"
