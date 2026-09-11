# 百万级写入（分批写入）

```java
@GetMapping("/export/large")
public void exportLarge(HttpServletResponse response) throws IOException {
    response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
    response.setCharacterEncoding("utf-8");
    String fileName = URLEncoder.encode("大数据", "UTF-8").replaceAll("\\+", "%20");
    response.setHeader("Content-disposition",
        "attachment;filename*=utf-8''" + fileName + ".xlsx");

    try (ExcelWriter excelWriter = EasyExcel.write(
            response.getOutputStream(), UserExportDTO.class).build()) {
        WriteSheet writeSheet = EasyExcel.writerSheet("用户列表").build();
        int pageSize = 2000;
        int totalPage = (int) Math.ceil((double) totalCount / pageSize);
        for (int pageNum = 1; pageNum <= totalPage; pageNum++) {
            List<UserExportDTO> pageData = userService.findPage(pageNum, pageSize);
            excelWriter.write(pageData, writeSheet);
            pageData.clear();
        }
    }
}
```

## 关键点

- 每次 `excelWriter.write()` 写一页数据（pageSize 2000 是常见值）
- 分页查询后 `clear()` 释放内存
- 用 try-with-resources 自动 `finish()` 关闭 `ExcelWriter`
- 一次性写入 < 5000 行推荐；> 5000 必须分批
