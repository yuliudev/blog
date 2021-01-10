## Dio 基础

dio 是一个强大的 Dart Http 请求库，支持 Restful API、 FormData、拦截器、请求取消、Cookie管理、文件上传/下载、超时和自定义适配器等。

### 引入和简单的 GET 请求

Flutter 提供了第三方包管理工具，就和前端经常使用的 npm 包管理类似。Dart 的包管理文件叫做 `pubspec.yaml` ，其实它统管整个项目，操作最多的就是第三方插件和静态文件（文件在项目根目录下）,如果要引入第三方包需要在 `dependencies` 里写明。

#### 添加 dio 依赖

例如要加入 dio ,代码如下

```
dependencies:
    dio: ^2.0.7
```

#### dio 发送 get 请求

引入 dio 库
```
import 'package:dio/dio.dart';
```

然后写一个基本 get 请求方法，暂时命名为 getHttp(),用了异步的方法，因为这样会防止后面的程序堵塞

```
void getHttp() async {  //异步请求
  try {
    Response response; //返回的对象
    // var data = {'': ''};
    response = await Dio().get(
      "http://106.14.115.151:8080/api/user/1",
      //queryParameters:data
    );
    return print(response);
  } catch (e) {
    return print(e);
  }
}
```
![](https://wx1.sinaimg.cn/mw1024/a6944a0egy1g1u47pn4j9j213i0frq3u.jpg)

### GET 请求和动态组件的协作

#### 生成动态组件

可以使用 `stful` 的快捷方式，在 VSCode 里快速生成 `StatefulWidget` 的基本结构

#### 加入 Widget

```
class MemberPage extends StatefulWidget {
  @override
  _MemberPageState createState() => _MemberPageState();
}

class _MemberPageState extends State<MemberPage> {
  TextEditingController typeController = TextEditingController();
  String showText = '返回值';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Dio GET 测试'),
        elevation: 0.0,
      ),
      body: Container(
        padding: EdgeInsets.all(20.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            TextField(
              controller: typeController, //得到文本框的值
              decoration: InputDecoration(
                contentPadding: EdgeInsets.all(10.0),
                labelText: 'ID',
                helperText: '在此输入用户 ID',
                icon: Icon(Icons.person),
              ),
              autofocus: false, //关闭自动聚焦，防止键盘自动打开
              keyboardType: TextInputType.number,
            ),
            RaisedButton(
              onPressed:  _submitAction,
              child: Text('提交'),
              color: Theme.of(context).backgroundColor,
            ),
            Text(
              showText,
              overflow: TextOverflow.clip,
              maxLines: 20,
            ),
          ],
        ),
      ),
    );
  }
}
```

#### dio 的 get 方法

布局完成后，可以先编写远程接口的调用方法，不过这里返回值为一个 `Future` ,这个对象支持一个等待回调方法 `then`

```
  Future getHttp(String typeText) async {
    try {
      Response response;
      response = await Dio().get(
        "http://106.14.115.151:8080/api/user/" + typeText
      );
      print(response);
      return response;
    } catch(e) {
      return print(e);
    }
  }
```

#### 处理得到的数据

当写完内容后，要点击按钮，按钮会调用方法，并进行一定的判断。比如判断文本框是不是为空。然后当后端返回数据时，用 setState 方法更新数据

```
  void _submitAction() {
    print("你按下了按钮！");
    if (typeController.text == '') {
      showDialog(
        context: context,
        builder: (context) => AlertDialog(
          title: Text('输入框不可为空！'),
        )
      );
    } else {
      getHttp(typeController.text).then((val){
        setState(() {
         showText = val.toString(); 
        });
      });
    }
  }
```

![](http://wx3.sinaimg.cn/mw690/a6944a0egy1g1u48gfyi9g20dc0nphdw.gif)

### POST 请求

其实 `post` 的使用非常简单，除了把原来的 `get` 换成 `post`，还需要改动一点地方

```
import 'package:flutter/material.dart';
import 'package:dio/dio.dart';

class MemberPage extends StatefulWidget {
  @override
  _MemberPageState createState() => _MemberPageState();
}

class _MemberPageState extends State<MemberPage> {
  TextEditingController mobileController = TextEditingController();
  TextEditingController pwdController = TextEditingController();

  String showText = '返回值';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Dio POST 测试'),
        elevation: 0.0,
      ),
      body: Container(
        padding: EdgeInsets.all(20.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            TextField(
              controller: mobileController, //得到文本框的值
              decoration: InputDecoration(
                contentPadding: EdgeInsets.all(10.0),
                labelText: '手机号',
                helperText: '在此输入手机号',
                icon: Icon(Icons.phone_android),
              ),
              autofocus: false, //关闭自动聚焦，防止键盘自动打开
              keyboardType: TextInputType.number,
            ),
            TextFormField(
              controller: pwdController, //得到文本框的值
              decoration: InputDecoration(
                  contentPadding: EdgeInsets.all(10.0),
                  labelText: '密码',
                  helperText: '在此输入密码',
                  icon: Icon(Icons.lock)),
              autofocus: false,
              obscureText: true,
            ),
            RaisedButton(
              onPressed: _submitAction,
              child: Text('登录'),
              color: Theme.of(context).backgroundColor,
            ),
            Text(
              showText,
              overflow: TextOverflow.clip,
              maxLines: 2,
            ),
          ],
        ),
      ),
    );
  }

  void _submitAction() {
    print("你按下了按钮！");
    if (mobileController.text == '' || pwdController.text == '') {
      showDialog(
          context: context,
          builder: (context) => AlertDialog(
                title: Text('输入框不可为空！'),
              ));
    } else {
      getHttp(mobileController.text, pwdController.text).then((val) {
        setState(() {
          showText = val['msg'].toString();
        });
      });
    }
  }

  Future getHttp(String mobile, String pwd) async {
    try {
      Response response;
      response = await Dio().post("http://106.14.115.151:8080/api/user/sign_in",
        data : {
          "mobile": mobile, 
          "password": pwd
        }
      );
      return response.data;
    } catch (e) {
      return print(e);
    }
  }
}
```
![](http://wx2.sinaimg.cn/mw690/a6944a0egy1g1u48f6n33g20dc0npu0z.gif)

### 伪造请求头获取数据

在很多时候，后端为了安全都会有一些请求头的限制，只有请求头对了，才能正确返回数据

#### 查看极客时间的数据端口

首先在 chrome 打开网站 https://time.geekbang.org ,然后按 F12 打开浏览器控制台，来到 NetWork 选项卡，再选择 XHR 选项卡，这时候刷新页面就会出现异步请求的数据。选择newAll这个接口来进行查看

拷贝地址：https://time.geekbang.org/serv/v1/column/newAll

现在以这个接口为案例，来获取它的数据

#### 非法请求的实现

新建一个文件夹，起名叫作 config，然后在里边新建一个文件 httpHeaders.dart,把请求头设置好，请求头可以在浏览器中轻松获得，获得后需要进行改造

```
const httpHeaders = {
  'Accept': 'application/json, text/plain, */*',
  'Accept-Encoding': 'gzip, deflate, br',
  'Accept-Language': 'zh-CN,zh;q=0.9',
  'Connection': 'keep-alive',
  'Content-Type': 'application/json',
  'Cookie': '_ga=GA1.2.676402787.1548321037; GCID=9d149c5-11cb3b3-80ad198-04b551d; _gid=GA1.2.359074521.1550799897; _gat=1; Hm_lvt_022f847c4e3acd44d4a2481d9187f1e6=1550106367,1550115714,1550123110,1550799897; SERVERID=1fa1f330efedec1559b3abbcb6e30f50|1550799909|1550799898; Hm_lpvt_022f847c4e3acd44d4a2481d9187f1e6=1550799907',
  'Host': 'time.geekbang.org',
  'Origin': 'https://time.geekbang.org',
  'Referer': 'https://time.geekbang.org/',
  'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36'
};
```

有了请求头文件后，可以引入请求头文件，并进行设置

```
import '../config/httpHeaders.dart';
dio.options.headers= httpHeaders;
```

全部代码

```
import 'package:flutter/material.dart';
import 'package:dio/dio.dart';
import '../../config/httpHeaders.dart';

class MemberPage extends StatefulWidget {
  @override
  _MemberPageState createState() => _MemberPageState();
}

class _MemberPageState extends State<MemberPage> {
  String showText = '还没有请求数据';
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('请求远程数据'),
      ),
      body: SingleChildScrollView(
        padding: EdgeInsets.all(10.0),
        child: Column(
          children: <Widget>[
            RaisedButton(
              onPressed: _jike,
              child: Text('请求数据'),
            ),
            Text(showText)
          ],
        ),
      ),
    );
  }

  void _jike() {
    print('开始向极客时间请求数据..................');
    getHttp().then((val) {
      setState(() {
        showText = val['data'].toString();
      });
    });
  }

  Future getHttp() async {
    try {
      Response response;
      Dio dio = new Dio();
      dio.options.headers = httpHeaders;
      response =
          await dio.get("https://time.geekbang.org/serv/v1/column/newAll");
      print(response);
      return response.data;
    } catch (e) {
      return print(e);
    }
  }
}
```

