# 图片导出

```java
@Getter
@Setter
@EqualsAndHashCode
@ContentRowHeight(100)
@ColumnWidth(100 / 8)
public class ImageDemoData {
    private File file;
    private InputStream inputStream;
    @ExcelProperty(converter = StringImageConverter.class)
    private String string;
    private byte[] byteArray;
    private URL url;
    private WriteCellData<Void> writeCellDataFile;  // 复杂图片（多图/带文字）
}
```

**多图嵌入同一单元格**：

```java
WriteCellData<Void> writeCellData = new WriteCellData<>();
imageDemoData.setWriteCellDataFile(writeCellData);
writeCellData.setType(CellDataTypeEnum.STRING);
writeCellData.setStringValue("额外的文字");

List<ImageData> imageDataList = new ArrayList<>();
ImageData imageData = new ImageData();
imageDataList.add(imageData);
writeCellData.setImageDataList(imageDataList);
imageData.setImage(FileUtils.readFileToByteArray(new File(imagePath)));
imageData.setImageType(ImageType.PICTURE_TYPE_PNG);
imageData.setTop(5);
imageData.setRight(40);
imageData.setBottom(5);
imageData.setLeft(5);
```

> **官方原文**："图片都会放到内存 暂时没有很好的解法，大量图片的情况下建议…将图片上传到oss…然后直接放链接"
