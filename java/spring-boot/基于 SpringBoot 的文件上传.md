## 基于 SpringBoot 的文件上传

上传文件是互联网中常常应用的场景之一，最典型的情况就是上传头像等。

主要有以下几种方式

* 直接上传到应用服务器

* 上传到 OSS 内容存储服务器（阿里云、七牛云）

* 前端将图片转成 Base64 编码上传

### 前后端不分离

#### 创建 upload 模块

![](https://upload-images.jianshu.io/upload_images/15141093-2dd829cc99c536d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 添加 web、thymeleaf 依赖

![](https://upload-images.jianshu.io/upload_images/15141093-da3f91522e6254e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 配置上传属性

配置上传属性 application.properties，指定上传文件大小限制等
```
#文件上传的配置
spring.servlet.multipart.max-file-size=10MB
```

#### 编写控制器

通过 `java.nio` 实现文件的上传
```
//这是一个上传文件控制器
@Controller
public class UploadController {
    //指定一个临时路径作为上传目录
    //private static String UPLOAD_FOLDER = "C:\\Users\\Desktop\\UPLOAD\\";

    //遇到http://localhost:8080，则跳转至upload.html页面
    @GetMapping("/")
    public String index() {
        return "upload";
    }

    @PostMapping("upload")
    public String fileUpload(@RequestParam("file") MultipartFile srcFile, RedirectAttributes redirectAttributes) {
        //前端没有选择文件，srcFile为空
        if (srcFile.isEmpty()) {
            redirectAttributes.addFlashAttribute("message", "请选择一个文件");
            return "redirect:upload_status";
        }
        //选择了文件，开始上传操作
        try {
            //构建上传目标路径，找到了项目的target的classes目录
            File destFile = new File(ResourceUtils.getURL("classpath:").getPath());
            if (!destFile.exists()) {
                destFile = new File("");
            }
            //输出目标文件的绝对路径
            System.out.println("file path:" + destFile.getAbsolutePath());
            //拼接子路径，以时间日期创建文件夹
            Date date = new Date();
            File upload = new File(destFile.getAbsolutePath(), "static/" + new SimpleDateFormat("yyyyMMdd/").format(date));
            //若目标文件夹不存在，则创建
            if (!upload.exists()) {
                upload.mkdirs();
            }
            //使用uuid重命名文件
            String fileName = srcFile.getOriginalFilename();
            String fileExtension = fileName.substring(fileName.lastIndexOf("."), fileName.length());
            System.out.println("完整的上传路径：" + upload.getAbsolutePath() + "/" + fileExtension);
            //根据srcFile大小，准备一个字节数组
            byte[] bytes = srcFile.getBytes();
            //拼接上传路径
            //Path path = Paths.get(UPLOAD_FOLDER + srcFile.getOriginalFilename());
            //通过项目路径，拼接上传路径
            Path path = Paths.get(upload.getAbsolutePath() + "/" + UUIDUtil.getUUID32() + fileExtension);
            //** 开始将源文件写入目标地址
            Files.write(path, bytes);
            redirectAttributes.addFlashAttribute("message", "文件上传成功" + UUIDUtil.getUUID32() + fileExtension);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "redirect:upload_status";
    }

    //匹配upload_status页面
    @GetMapping("/upload_status")
    public String uploadStatusPage() {
        return "upload_status";
    }
}
```

工具类生成 UUID 重命名文件
```
public class UUIDUtil {
    public static String getUUID32() {
        String uuid = UUID.randomUUID().toString().replace("-", "").toLowerCase();
        return uuid;
    }
}
```
#### 控制器配置好 thymeleaf 的页面跳转及信息显示

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Spring Boot 文件上传</title>
</head>
<body>
<form method="post" action="/upload" enctype="multipart/form-data">
    <input type="file" name="file">
    <input type="submit" value="上传">
</form>
</body>
</html>
```
```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>文件上传状态显示</title>
</head>
<body>
<h2>Spring Boot 的文件显示状态</h2>
<div th:if="${message}">
    <h2 th:text="${message}"/>
</div>
</body>
</html>
```

#### 运行项目，上传文件，观察结果

![](https://upload-images.jianshu.io/upload_images/15141093-a10897d23ed03d7d.gif?imageMogr2/auto-orient/strip)

### 前后端分离

#### 后端

只需要将控制器的注解改为 `@RestController`

#### 前端

```
<template>
  <div class="container">
    <h2>任务中心</h2>
    <div class="preview"><img :src="imgUrl"></div>
    <form>
      <div class="upload">
        选择文件
        <input type="file" @change="getFile($event)">
      </div>
      <br>
      <button @click="submit($event)" class="sub-btn">提交</button>
    </form>
  </div>
</template>


<script>
export default {
  name: "Task",
  data() {
    return {
      imgUrl:
        "https://static-cdn-oss.mosoteach.cn/mssvc/icons/icon_cc_cover1@2x.png",
      file: ""
    };
  },
  methods: {
    getFile: function(event) {
      this.file = event.target.files[0];
      var windowURL = window.URL || window.webkitURL;
      alert(windowURL.createObjectURL(this.file));
      this.imgUrl = windowURL.createObjectURL(this.file);
    },
    submit: function(event) {
      event.preventDefault();
      let formData = new FormData();
      formData.append("file", this.file);
      this.$http
        .post("http://localhost:8081/upload", formData)
        .then(function(response) {
          alert(response.data);
          this.imgUrl = response.data;
        });
    }
  }
};
</script>
```