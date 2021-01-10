## 使用 JavaMailSender 发送邮件

Spring 提供了非常好用的 JavaMailSender 接口实现邮件发送。在 Spring Boot 的 Starter 模块中也为此提供了自动化配置。

首先配置QQ邮箱->设置->账户->开启服务POP3/SMTP开启->获取授权码

#### 添加 pom 依赖

```
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

#### 修改配置文件

完成了依赖引入之后，只需要在 `application.properties` 中配置相应的属性内容

```
spring.mail.host=smtp.qq.com
spring.mail.username=xxx@qq.com
spring.mail.password=授权码
spring.mail.default-encoding=UTF-8


##如果不加下面3句,会报530错误
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
spring.mail.properties.mail.smtp.starttls.required=true
```

#### Service 接口

```
public interface MailService {
    void sendMail(String to,String subject,String content);
}
```

#### 实现接口

```
@Service("mailService")
public class MailServiceImpl implements MailService {
    @Autowired
    private JavaMailSender mailSender;

    @Override
    public void sendMail(String to, String subject, String content) {
        SimpleMailMessage mailMessage=new SimpleMailMessage();
        mailMessage.setFrom("1062273622@qq.com");//发起者
        mailMessage.setTo(to);//接受者
        mailMessage.setSubject(subject);
        mailMessage.setText(content);
        try {
            mailSender.send(mailMessage);
            System.out.println("发送简单邮件");
        }catch (Exception e){
            System.out.println("发送简单邮件失败");
        }
    }
}
```

> 由于 Spring Boot 的 starter 模块提供了自动化配置，所以在引入了 spring-boot-starter-mail 依赖之后，会根据配置文件中的内容去创建 JavaMailSender 实例，因此我们可以直接在需要使用的地方直接 @Autowired 来引入邮件发送对象

#### 发送附件

通过 `MimeMessageHelper` 来发送一封带有附件的邮件

```
@Override
public void sendMimeMail(String to, String subject, String content, File file) throws Exception{
    MimeMessage mimeMessage = mailSender.createMimeMessage();

    MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);
    helper.setFrom("303883148@qq.com");
    helper.setTo(to);
    helper.setSubject(subject);
    helper.setText(content);
    helper.addAttachment(file.getName(), file);
    mailSender.send(mimeMessage);
}
```

#### Controller 层

```
@RestController
@RequestMapping(value = "/api/mail")
public class MailController {
    @Resource
    private MailService mailService;

    @PostMapping("/simplemail")
    public ResponseResult sendSimpleEMail(@RequestParam("to") String to,
                                          @RequestParam("subject") String subject,
                                          @RequestParam("content") String content) {
        mailService.sendSimpleMail(to, subject, content);
        return ResponseResult.success();
    }

    @PostMapping("/mimemail")
    public ResponseResult sendMimeEMail(@RequestParam("to") String to,
                                        @RequestParam("subject") String subject,
                                        @RequestParam("content") String content,
                                        @RequestParam("file") MultipartFile file) throws Exception {
        File tempFile = File.createTempFile(file.getOriginalFilename(), file.getOriginalFilename());
        // MultipartFile to File
        file.transferTo(tempFile);
        mailService.sendMimeMail(to, subject, content, tempFile);
        return ResponseResult.success();
    }
}
```





