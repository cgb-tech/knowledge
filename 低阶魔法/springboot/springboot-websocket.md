首先是添加的依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

之后是config的配置 名字为 `SocketConfig`

```java
@Configuration
@EnableWebSocket
public class SocketConfig {

    @Bean
    public ServerEndpointExporter serverEndpointExporter(){
        return new ServerEndpointExporter();
    }

}
```

`websocket`的核心类 `ChatSocket`

```java
@ServerEndpoint("/chatSocket/{id}")
@Service
public class ChatSocket {

    //定义了所有的用户
    private static CopyOnWriteArraySet<ChatSocket> webSocketSet = new CopyOnWriteArraySet<>();
    private static Map<String, Session> sessionMap = new ConcurrentHashMap<>();
    private Session session;

    @OnOpen
    public void onOpen(@PathParam("id") String id, Session session) {
        sessionMap.put(id, session);
        this.session = session;
        webSocketSet.add(this);
        sendMessageToAll("我来了,大家" + "我是 " + id);
        System.out.println(id + "连接上了 ");
    }

    @OnMessage
    public void onMessage(String message, @PathParam("id") String id) {
        System.out.println("在发消息");
        sendMessageToAll(message + id);
    }


    @OnError
    public void onError(Session session, Throwable throwable) {
        throwable.printStackTrace();
    }

    @OnClose
    public void onClose(Session session) {
        webSocketSet.remove(this);
    }


    public void sendMessage(Session session, String message) {
        try {
            session.getBasicRemote().sendText(message);
            System.out.println("發送了消息" + message);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void sendMessageToAll(String message1) {
        // String message = message1;
        // webSocketSet.forEach((chatSocket) -> {
        //     sendMessage(chatSocket.session, message);
        // });
        for (ChatSocket server : webSocketSet) {
            sendMessage(server.session,message1);
        }
    }

}
```

最后是建议的测试是否连接的通的前端页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>new </title>
</head>
<body>

<input type="text" id="sid" >
<button onclick="clickdb()" value="點我">點我啊</button>
<script !src="">

    var host = window.location.host;
    var url = "ws://"+host+"/chatSocket/12" ;
    var ws = null;
    ws = new WebSocket(url);
    ws.onopen = function () {
        console.log("建立 websocket 连接...");
        alert('開始建立')
    };
    function clickdb () {
        var message = document.getElementById('sid').value
        ws.send(message);
    }
    ws.onmessage = function(evn){
        //转换为json字符串
        alert(evn.data)
    }


</script>
</body>
</html>
```

下面的是一个controller,用来设置首页的跳转

```java
@Controller
public class SocketController {

    @RequestMapping("/index")
    public String index(){
        return "new";
    }
}

```

之后的功能需要自己的扩展