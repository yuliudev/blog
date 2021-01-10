## 使用 WebSocket

WebSocket 协议是基于 TCP 的一种新的网络协议。它实现了浏览器与服务器全双工 (full-duplex) 通信 —— 允许服务器主动发送信息给客户端。

![](http://www.runoob.com/wp-content/uploads/2016/03/ws.png)

当我们想要查询当前的排队情况，只能是页面轮询向服务器发出请求，服务器返回查询结果。轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）。因此 WebSocket 就是这样发明的。

#### 添加 pom 依赖

SpringBoot 2.0 对 WebSocket 支持，可以直接使用包引入

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

#### WebSocketConfig

启用 WebSocket 的支持

```
/**
 * WebSocket的配置类
 * 开启了WebSocket支持
 */
@Configuration
public class WebSocketConfig {
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}
```

#### WebSocketServer

因为 WebSocket 是类似客户端服务端的形式(采用 ws 协议)，那么这里的 `WebSocketServer` 其实就相当于一个 ws 协议的 Controller，直接 `@ServerEndpoint("/websocket")@Component` 启用即可，然后在里面实现 `@OnOpen`, `@onClose`, `@onMessage` 等方法

```
//客户端向服务器端建立WebSocket连接的url
@ServerEndpoint("/websocket")
@Component
public class WebSocketServer {

    //静态变量，用来记录当前在线连接数,可选
    private static int onlineCount = 0;

    //concurrent包的线程安全Set，用来存放每个客户端对应的MyWebSocket对象，必须
    private static CopyOnWriteArraySet<WebSocketServer> webSocketSet
            = new CopyOnWriteArraySet<WebSocketServer>();

    //与某个客户端的连接会话，需要通过它来给客户端发送数据,必须
    private Session session;


    /**
     * 连接建立成功调用的方法
     */
    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        webSocketSet.add(this);     //将客户端加入set中
        addOnlineCount();           //在线数加1
        System.out.println("有新窗口开始监听,当前在线人数为"
                + getOnlineCount());
        try {
            sendMessage("连接成功");
        } catch (IOException e) {
            System.out.println("WebSocket IO异常");
        }
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose() {
        webSocketSet.remove(this);  //从set中删除
        subOnlineCount();           //在线数减1
        System.out.println("有连接关闭！当前在线人数为" + getOnlineCount());
    }

    /**
     * 收到客户端消息后调用的方法
     *
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message, Session session) {
        System.out.println("收到客户端的信息:" + message);
        //群发消息
        for (WebSocketServer item : webSocketSet) {
            try {
                item.sendMessage(message);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * @param session
     * @param error
     */
    @OnError
    public void onError(Session session, Throwable error) {
        System.out.println("发生错误");
        error.printStackTrace();
    }

    /**
     * 实现服务器主动推送
     */
    public void sendMessage(String message) throws IOException {
        this.session.getBasicRemote().sendText(message);
    }


    /**
     * 群发自定义消息
     */
    public static void sendInfo(String message) throws IOException {
        System.out.println("推送消息内容:" + message);
        for (WebSocketServer item : webSocketSet) {
            try {
                item.sendMessage(message);
            } catch (IOException e) {
                continue;
            }
        }
    }

    public static synchronized int getOnlineCount() {
        return onlineCount;
    }

    public static synchronized void addOnlineCount() {
        WebSocketServer.onlineCount++;
    }

    public static synchronized void subOnlineCount() {
        WebSocketServer.onlineCount--;
    }
}
```

#### 消息推送

实现推送新信息，可以在 Controller层 写个方法调用 `WebSocketServer.sendInfo();` 即可

```
@RestController
public class WebSocketController {

    //推送数据接口
    @GetMapping("/socket/push")
    public String pushMsg(String message) {
        try {
            WebSocketServer.sendInfo(message);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "success";
    }
}
```

#### 页面发起 socket 请求

在页面用 js 代码调用 socket，协议是 ws

```
<!DOCTYPE html>
<html>

<head>
	<meta charset="utf-8" />
	<title>websocket示例</title>
	<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>

<body>
	<div id="app">
		<h3>消息显示</h3>
		<ul>
			<li v-for="(message, index) in messages" :key="index">
				{{message}}
			</li>
		</ul>
		<hr>
		<h3>发送消息 </h3>
		<input type="text" v-model="sendMsg" />
		<button type="button" @click="send">发送</button>
	</div>

	<script type="text/javascript">
		var socket;
		var app = new Vue({
			el: '#app',
			data: {
				messages: [],
				sendMsg: '',
				
			},
			created: function () {
				var _this = this;
				//创建WebSocket对象，指定要连接的服务器地址和端口，建立连接
				socket = new WebSocket("ws://localhost:8080/websocket");
				//打开连接
				socket.onopen = function () {
					console.log("Socket已打开");

				};
				//获得服务端推送的消息
				socket.onmessage = function (msg) {
					console.log(msg.data);
					_this.messages.push(msg.data);
					console.log(_this.messages);

				};
				//关闭连接
				socket.onclose = function () {
					console.log("Socket已关闭");
				};
				//发送错误
				socket.onerror = function () {
					alert("Socket发生了错误");
				}
			},
			watch: {
				// 如果 `messages` 发生改变，这个函数就会运行
				messages: function (newMsg, oldMsg) {
					this.messages = newMsg;
				},
			},
			methods: {
				send: function () {
					socket.send(this.sendMsg);
				}
			}
		})
	</script>
</body>

</html>
```

