# 웹 소켓을 왜 쓰는가?
일반적인 get, post와 같은 HTTP 통신은 클라이언트에서 단방향으로 통신을 보내 응답을 받는 형태다.

하지만 채팅, 주식등과 같은 실시간 데이터는 서버에서만 보내도 된다. 이떄 소켓을 사용한다.

<br><br>

# 웹 소켓
핸드쉐이킹은 GET으로 보내야 하면 HTTP 1.1이상이 "필수"다.

헤더는 아래와 같다. ws패키지에서 알아서 설정해준다.
```
Upgrade: websocket
Connetion: Upgrade
를 이용해서 프로토콜을 소켓으로 변경해야 한다.

Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
클라이언트가 요청하는 하위 프로토콜로, 우선순위 부여, 서버에서 여러 프로토콜, 버전을 나눠서 서비스할 때 필요한 정보다.
```

<br><br>

# import
```
npm i ws
```

<br><br>

# 소스 설명
server.js
```
const WebSocket = require('ws');
const server = new WebSocket.Server({ port: 3000 });

server.on('connection', ws => {
    ws.send('[서버 접속 완료]');

    ws.on('message', message => {
        ws.send(`서버로부터 응답: ${message}`);
    });

    ws.on('close', () => {
        console.log('클라이언트 접속 해제');
    });
});
```
서버의 소스는 위와 같고, 클라이언트로 부터 접속, 메시지가 오면 응답하고, 종료는 콘솔로 찍는다.

<br>

client.html
```
<body>
    <textarea id="message" cols="50" rows="5"></textarea>
    <br />

    <button onclick="sendMessage()">전송</button>
    <button onclick="webSocketClose()">종료</button>
    <div id="messages"></div>
</body>

<script>
    const ws = new WebSocket('ws://localhost:3000');

    function sendMessage(){
        ws.send(document.getElementById('message').value);
    }

    function webSocketClose(){
        console.log("종료 누름");
        ws.close();
    }

    ws.onopen = function() {
        console.log("클라이언트 접속 완료!");
    };

    ws.onmessage = function(event){
        let message = event.data.replace(/(\r\n|\n|\r)/g,"<br />");
        let el = document.createElement('div');
        el.innerHTML = message;
        el.className = 'message';

        document.getElementById('messages').append(el);
    }

    ws.onclose = function(e){
        console.log("종료");
        document.getElementById('messages').append("서버 접속 종료");
    }

</script>
```
new WebSocket으로 소켓정보를 저장해서 메시지 전송, 종료를 실행한다.

페이지가 열리면 자동으로 소켓연결을 시도하고, 

sendMessage()를 이용해서 메시지를 전송한다.

webSocketClose()를 이용해서 소켓을 종료한다.

<br><br>

# 테스트
```
1. 받아서 ws 패키지 설치 (package.json에 포함 되어있다.)
2. node server.js 로 서버 실행
3. 브라우저에서 client.html 실행 (브라우저에 해당파일 드래그 하면 된다.)
```

<br><br><br><br><br><br>
출처 - Node.js 백엔드 개발자 되기 (저자 : 박승규)