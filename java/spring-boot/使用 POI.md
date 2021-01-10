## 使用 POI

在办公系统中，常常有需要导入、导出 Excel 的场景，在 SpringBoot 中可以使用 POI，快速实现 Excel 的导入导出。

用 POI 操作 Excel 时，我们会考虑到 Excel 版本及数据量的问题。针对不同的 Excel 版本，要采用不同的工具类

* HSSFWorkbook:是操作 Excel2003 以前（包括2003）的版本，扩展名是`.xls`

* XSSFWorkbook:是操作 Excel2007 的版本，扩展名是`.xlsx`

对于不同版本的 EXCEL 文档要使用不同的工具类，虽然类名不同，但是方法基本都是一致的，因此这里将只介绍 `.xls` 一种

首先在 `pom.xml` 文件中添加依赖

```
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.17</version>
</dependency>
```

### 导出 Excel

#### 创建 Excel 文档

```
HSSFWorkbook workbook = new HSSFWorkbook();
```

#### 设置文档的基本信息，这一步是可选的

```
//获取文档信息，并配置
DocumentSummaryInformation dsi = workbook.getDocumentSummaryInformation();
//文档类别
dsi.setCategory("文章信息");
//设置文档管理员
dsi.setManager("XXX");
//设置组织机构
dsi.setCompany("JIANYUE APP");
//获取摘要信息并配置
SummaryInformation si = workbook.getSummaryInformation();
//设置文档主题
si.setSubject("文章信息表");
//设置文档标题
si.setTitle("文章信息");
//设置文档作者
si.setAuthor("XXX");
//设置文档备注
si.setComments("备注信息暂无");
```

#### 创建一个 Excel 表单

```
HSSFSheet sheet = workbook.createSheet("JIANYUE APP文章信息表");
```

#### 创建一行

0 表示第一行

```
HSSFRow headerRow = sheet.createRow(0);
```
#### 在第一行中创建第一个单元格，并设置数据

```
HSSFCell cell0 = headerRow.createCell(0);
cell0.setCellValue("编号");
```

#### 将 Excel 写到 ByteArrayOutputStream 中

```
baos = new ByteArrayOutputStream();
workbook.write(baos);
```

#### 创建 ResponseEntity 并返回

```
return new ResponseEntity<byte[]>(baos.toByteArray(), headers, HttpStatus.CREATED);
```

#### 一个导出文章信息的 Demo

##### PoiUtils

```
import com.springboot.jianyue.api.entity.Article;
import com.springboot.jianyue.api.entity.User;
import com.springboot.jianyue.api.util.StringUtil;
import org.apache.poi.hpsf.DocumentSummaryInformation;
import org.apache.poi.hpsf.SummaryInformation;
import org.apache.poi.hssf.usermodel.*;
import org.apache.poi.poifs.filesystem.POIFSFileSystem;
import org.apache.poi.ss.usermodel.FillPatternType;
import org.apache.poi.ss.usermodel.IndexedColors;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.multipart.MultipartFile;

import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class PoiUtils {
    public static ResponseEntity<byte[]> exportArticleExcel(List<Article> articleList){
        HttpHeaders headers = null;
        ByteArrayOutputStream baos = null;

        try {
            //1.创建Excel文档
            HSSFWorkbook workbook = new HSSFWorkbook();
            //2.创建文档摘要
            workbook.createInformationProperties();
            //3.获取文档信息，并配置
            DocumentSummaryInformation dsi = workbook.getDocumentSummaryInformation();
            //3.1文档类别
            dsi.setCategory("文章信息");
            //3.2设置文档管理员
            dsi.setManager("XXX");
            //3.3设置组织机构
            dsi.setCompany("JIANYUE APP");
            //4.获取摘要信息并配置
            SummaryInformation si = workbook.getSummaryInformation();
            //4.1设置文档主题
            si.setSubject("文章信息表");
            //4.2.设置文档标题
            si.setTitle("文章信息");
            //4.3 设置文档作者
            si.setAuthor("XXX");
            //4.4设置文档备注
            si.setComments("备注信息暂无");
            //创建Excel表单
            HSSFSheet sheet = workbook.createSheet("JIANYUE APP文章信息表");
            //创建日期显示格式
            HSSFCellStyle dateCellStyle = workbook.createCellStyle();
            dateCellStyle.setDataFormat(HSSFDataFormat.getBuiltinFormat("m/d/yy"));
            //创建标题的显示样式
            HSSFCellStyle headerStyle = workbook.createCellStyle();
            headerStyle.setFillForegroundColor(IndexedColors.YELLOW.index);
            headerStyle.setFillPattern(FillPatternType.SOLID_FOREGROUND);

            //定义列的宽度
            sheet.setColumnWidth(0, 5 * 256);
            sheet.setColumnWidth(1, 12 * 256);
            sheet.setColumnWidth(2, 10 * 256);
            sheet.setColumnWidth(3, 5 * 256);
            sheet.setColumnWidth(4, 12 * 256);
            sheet.setColumnWidth(5, 20 * 256);

            //5.设置表头
            HSSFRow headerRow = sheet.createRow(0);
            HSSFCell cell0 = headerRow.createCell(0);
            cell0.setCellValue("文章ID");
            cell0.setCellStyle(headerStyle);
            HSSFCell cell1 = headerRow.createCell(1);
            cell1.setCellValue("作者ID");
            cell1.setCellStyle(headerStyle);
            HSSFCell cell2 = headerRow.createCell(2);
            cell2.setCellValue("文章标题");
            cell2.setCellStyle(headerStyle);
            HSSFCell cell3 = headerRow.createCell(3);
            cell3.setCellValue("文章内容");
            cell3.setCellStyle(headerStyle);
            HSSFCell cell4 = headerRow.createCell(4);
            cell4.setCellValue("创建日期");
            cell4.setCellStyle(headerStyle);

            //6.装数据
            for (int i = 0; i < articleList.size(); i++) {
                HSSFRow row = sheet.createRow(i + 1);
                Article article = articleList.get(i);
                row.createCell(0).setCellValue(article.getId());
                row.createCell(1).setCellValue(article.getUId());
                row.createCell(2).setCellValue(article.getTitle());
                row.createCell(3).setCellValue(article.getContent());
                HSSFCell createTimeCell = row.createCell(4);
                createTimeCell.setCellValue(article.getCreateTime());
                createTimeCell.setCellStyle(dateCellStyle);
            }

            headers = new HttpHeaders();
            headers.setContentDispositionFormData("attachment",
                    new String("articleTable.xls".getBytes("UTF-8"), "iso-8859-1"));
            headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
            baos = new ByteArrayOutputStream();
            workbook.write(baos);
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("excel导出成功");
        return new ResponseEntity<byte[]>(baos.toByteArray(), headers, HttpStatus.CREATED);
    }
}
```

##### ArticleController

```
@RequestMapping(value = "/exportArticle", method = RequestMethod.GET)
public ResponseEntity<byte[]> exportArticle() {
    return PoiUtils.exportArticleExcel(articleService.selectAllArticle());
}
```

### 导入 Excel

在此使用一个导入用户信息的例子

#### 编写底层批量插入方法

```
@InsertProvider(type = Provider.class, method = "batchInsert")
int batchInsert(List<User> user);

class Provider {
    /* 批量插入 */
    public String batchInsert(Map map) {
        List<User> users = (List<User>) map.get("list");
        StringBuilder sb = new StringBuilder();
        sb.append("INSERT INTO t_user (mobile,password,nickname,avatar,regtime,status) VALUES ");
        MessageFormat mf = new MessageFormat(
                "(#'{'list[{0}].mobile}, #'{'list[{0}].password}, #'{'list[{0}].nickname}, #'{'list[{0}].avatar}, #'{'list[{0}].regtime}, #'{'list[{0}].status} )"
        );
        for (int i = 0; i < users.size(); i++) {
            sb.append(mf.format(new Object[]{i}));
            if (i < users.size() - 1)
                sb.append(",");
        }
        return sb.toString();
    }
}
```

service 层代码省略

#### Excel 解析

将上传到的 MultipartFile 转为输入流，然后交给 POI 解析即可

##### 创建 HSSFWorkbook 对象

```
HSSFWorkbook workbook = new HSSFWorkbook(new POIFSFileSystem(file.getInputStream()));
```

##### 获取 sheet 数目，然后遍历

```
int numberOfSheets = workbook.getNumberOfSheets();
for (int i = 0; i < numberOfSheets; i++) {
    HSSFSheet sheet = workbook.getSheetAt(i);
    //...
}
```

##### 获取行数，然后遍历

注意第一行为标题

```
int physicalNumberOfRows = sheet.getPhysicalNumberOfRows();
Employee employee;
for (int j = 0; j < physicalNumberOfRows; j++) {
    if (j == 0) {
        continue;//标题行
    }
    //...
}
```

##### 获取单元格数，遍历单元格

```
int physicalNumberOfCells = row.getPhysicalNumberOfCells();
employee = new Employee();
for (int k = 0; k < physicalNumberOfCells; k++) {
    HSSFCell cell = row.getCell(k);
    //...
}
```

##### 完整代码

```
public static List<User> importUserExcel(MultipartFile file) {
    List<User> users = new ArrayList<>();
    try {
        HSSFWorkbook workbook = new HSSFWorkbook(new POIFSFileSystem(file.getInputStream()));
        int numberOfSheets = workbook.getNumberOfSheets();
        for (int i = 0; i < numberOfSheets; i++) {
            HSSFSheet sheet = workbook.getSheetAt(i);
            int physicalNumberOfRows = sheet.getPhysicalNumberOfRows();
            User user;
            for (int j = 0; j < physicalNumberOfRows; j++) {
                if (j == 0) {
                    continue;//标题行
                }
                HSSFRow row = sheet.getRow(j);
                if (row == null) {
                    continue;//没数据
                }
                int physicalNumberOfCells = row.getPhysicalNumberOfCells();
                user = new User();
                for (int k = 0; k < physicalNumberOfCells; k++) {
                    //System.out.println("CELL:" + physicalNumberOfCells);
                    //System.out.println("ROWS:" + physicalNumberOfRows);
                    HSSFCell cell = row.getCell(k);
                    switch (cell.getCellTypeEnum()) {
                        case STRING: {
                            String cellValue = cell.getStringCellValue();
                            if (cellValue == null) {
                                cellValue = "";
                            }
                            switch (k) {
                                case 0:
                                    user.setMobile(cellValue);
                                    break;
                                case 1:
                                    user.setPassword(StringUtil.getBase64Encoder(cellValue));
                                    break;
                                case 2:
                                    user.setNickname(cellValue);
                                    break;
                                case 3:
                                    user.setAvatar(cellValue);
                                    break;
                            }
                        }
                        break;
                        default: {
                            switch (k) {
                                case 4:
                                    user.setRegtime(cell.getDateCellValue());
                                    break;
                                case 5:
                                    user.setStatus((short) cell.getNumericCellValue());
                                    break;
                            }
                        }
                        break;
                    }
                }
                users.add(user);
            }
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    return users;
}
```

#### 控制层

```
@RequestMapping(value = "/importUser", method = RequestMethod.POST)
public ResponseResult importEmp(MultipartFile file) {
    List<User> users = PoiUtils.importUserExcel(file);
    if (userService.insertUsers(users) == users.size()) {
        return ResponseResult.success();
    }
    return ResponseResult.error(StatusConst.IMPORT_ERROR, MsgConst.IMPORT_ERROR);
}
```





