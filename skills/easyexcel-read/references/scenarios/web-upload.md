# Web 上传读取

```java
@PostMapping("upload")
@ResponseBody
public String upload(MultipartFile file) throws IOException {
    EasyExcel.read(file.getInputStream(), UploadData.class,
                   new UploadDataListener(uploadDAO)).sheet().doRead();
    return "success";
}
```

> **官方原文**："上传文件以 InputStream 形式读取"

## 注意事项

- `MultipartFile.getInputStream()` 需要关闭 — 用 try-with-resources 包裹避免句柄泄漏。
- 监听器**不能**被 Spring 管理（必须 `new`，Spring Bean 通过构造方法传入）。
- 大文件不要走 `doReadSync()`，必须用监听器流式读取。
