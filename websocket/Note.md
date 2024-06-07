# WebSocket[^1]
[^1]: Actually websocket is web and socket.
### Sences
- Server actively send message to client.
### History
1. Server can't actively send to client.
2. Server send response only.
3. We send requests Periodically to server to achieve the function of pushing message actively.
### How to apply
1. Use websocket framework from github.
2. Achieve websocket base on TCP protocol.
### What is the difference between http and websocket
1. These are separate protocols.
2. Websocket is compatible with http.
3. Websocket not need disconnect with server and client.
### Ex. (base on Flask)
- step1, we need install python websocket library as follows.
    ```shell
    pip3 install gevent-websocket
    ```
- step2, write python code to achieve websocket
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
##### Ex. We will implement websocket by using socket
###### This is a server as follows.
```python
import socket
import base64

def send_msg(conn, msg_bytes):
    import struct
    token = b"\x81"
    length = len(msg_bytes)
    if length < 126:
        token += struct.pack("B", length)
    elif length <= 0xFFFF:
        token += struct.pack("!BH", 126, length)
    else:
        token += struct.pack("!BQ", 127, length)

    msg = token + msg_bytes
    conn.send(msg)
    return True

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('127.0.0.1', 8002))
socket.listen(5)
# Waiting user connect
conn, address =sock.accept()
# Receive TLS data
msg = conn.recv(8096) # 'msg' is similar to http request header
msg_dict = {}
for item in msg.strip().split("\r\n"):
    msg_dict[item.strip().split(":")[0]] = item.strip().split(":")[0]
# We can find an attribute 'Sec-WebSocket-Key' in msg_dict, we need it's value.
key = msg_dict['Sec-WebSocket-Key']
# Next, we need encrypt key.
# The encryption as follows is world recognized, and the 'magic_string' is immutable.
magic_string = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11'
value = key + magic_string
ac = base64.b64encode(hashlib.sha1(value.encode('utf-8')).digest())
# We need send response to client, this is the content as response.
response_tpl = "HTTP/1.1 101 Switching Protocols\r\n" \
    "Upgrade:websocket\r\n" \
    "Connection:Upgrade\r\n" \
    "Sec-WebSocket-Accept:%s\r\n" \
    "WebSocket-Location:ws://127.0.0.1:8002\r\n\r\n"
response = response_tpl %(ac.decode('utf-8'), )
# We have finished the connection and can start connecting.
conn.send(response.encode('utf-8'))

# We need to receive messages sent by client
while True:
    data = conn.recv(8096)
    # We need to encrypt messages when server send to client
    payload_len = data[1] & 127 # First we need get the second byte and do AND operation with 127 to get last seven bits.
    if payload_len== 127:
        extend_payload_len = data[2:10]
        mask = data[10:14]
        decoded = data[14:]
    elif payload_len == 126:
        extend_payload_len = data[2:4]
        mask = data[4:8]
        decoded = data[8:]
    else:
        extend_payload_len = None
        mask = data[2:6]
        decoded = data[6:]

    bytes_list = bytearray()
    for i in range(len(decoded)):
        chunk = decoded[i] ^ mask[i % 4]
        bytes_list.append(chunk)
    body = str(bytes_list, encoding='utf-8')

    send_msg(conn, b"123")
```
##### Summary
1. Run server wait client connecting.
2. Server agrees connection when client connecting.
3. Client sends TLS message immediately.
4. Server encrypts received data after reveive TLS message.
5. Now, server and client can communicate with each other.

- When clients send message to the server, the server needs dencrypt data.
- The server still needs encrypt message to send to client as shown above.

### Interview Questions
- What is WebSocket?
    > It is a procotol, defined:
    > It needs TLS data when connecting.
    > It needs encrypt data when send data.
    > It doesn't disconnect after connected.
- What's the mean of websocket?
    > It allows the server send to clients actively
- What are its disadvantages?
    > It is not compatilble with older browsers.
- Which frameworks support websocket?
    > 1.The flask not support websocket, but can install gevent-websocket to use it.
    > 2.The django still not support websocket, you need install channel.
    > 3.The torando framework natively supports websocket.