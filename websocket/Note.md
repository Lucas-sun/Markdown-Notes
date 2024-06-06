# WebSocket[^1]
[^1]: Actually websocket is web and socket.
### Sences
- Server send message to client actively.
### History
1. Server can't send to client.
2. Server send response only.
3. We use action auto send scheduled requests to server for achieve result push message actively.
### How to apply
1. Use framework websocket from github.
2. Achieve websocket base on protocol TCP.
### What is the difference between http and websocket
1. These are separate protocols.
2. Websocket is compatible with http.
3. Websocket not need disconnect with server and client.
### Ex. (base on Flask)
- step1, we need install python library websocket as follows.
    ```shell
    pip3 install gevent-websocket
    ```
- step2, write python code for achieve websocket
    ```python
    from flask import Flask, render_template, request
    from geventwebsocket.handler import WebSocketHandler
    from gevent.pywsgi import WSGIServer

    app = Flask(__name__)

    @app.route('/index')
    def index():
        return render_template('index.html')

    WEBSOCKET_LIST = []
    @app.route('/message')
    def message():
        # If the request protocol is websocket, 'environ' have property 'wsgi.websocket' or other is None.
        ws = request.environ.get('wsgi.websocket')
        if not ws:
            print("http")
            return "You are using http protocol now."
        
        # We need record who connect websocket.
        WEBSOCKET_LIST.append(ws)
        # If protocol is websocket, python will run loop code for receive message sent by client, we can send response to client after.
        while True:
            message = ws.receive() # reveive
            # The message is None if client close, we need remove this connect.
            if not message:
                WEBSOCKET_LIST.remove(ws)
                ws.close()
                break
            print(message)
            ws.send(message) # push action(we can do other action)

    if __name__ == "__main__":
        # app.run() We use this method to start server before.
        # The normal request will redirect to app and websocket request will use WebSocketHandler to handle when use below method
        http_server = WSGIServer(('127.0.0.1', 5000), app, handler_class=WebSocketHandler)
        http_server.serve_forever()
    ```
### Principle of websocket
##### Ex. We will implementing websocket by using socket
```python
import socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('127.0.0.1', 8002))
socket.listen(5)
# Waiting user connect
conn, address =sock.accept()
# Receive TLS info
msg = conn.recv(8096)
```