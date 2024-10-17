### Concurrency and Networking
1. The difference between processes, threads and coroutines
2. GIL lock
3. OSI7 Layer
4. Three handshakes and four waves
5. TCP and UDP

### High Performance
- Q: Please download 10 pictures by urls provided.
  1. Serial execution
        ```python
        import requests

        urls = ['xxx', 'xxx', 'xxx']
        for url in urls:
            response = requests.get(url)
            with open("xxx.png", "wb") as f:
                f.write(response.content)
        ```
  2. Multithreading
        ```python
        # Thread utilization is not high.
        import requests
        import threading

        urls = ['xxx', 'xxx', 'xxx']

        def task(url):
            response = requests.get(url)
            with open("xxx.png", "wb") as f:
                f.write(response.content)

        for url in urls:
            t = threading.Thread(target=task, args=(url,))
            t.start()
        ```
  3. Coroutines
        ```python
        # pip3 install gevent
        # The gevent will transfer greenlet to achieve coroutines.

        from gevent import monkey; monkey.patch_all()
        import gevent
        import requests

        urls = ['xxx', 'xxx', 'xxx']

        def f(url):
            response = requests.get(url)
            with open("xxx.png", "wb") as f:
                        f.write(response.content)

        spawn_list = []
        for url in urls:
            spawn_list.append(gevent.spawn(func, url))

        gevent.joinall(spawn_list)
        ```
  4. Coroutines base on event loop non-blocking
     - twisted
        ```python
        from twisted.web.client import getPage, defer
        from twisted.internet import reactor

        def stop_loop(arg):
            reactor.stop()

        def get_response(contents):
            response = requests.get(url)
                with open("xxx.png", "wb") as f:
                    f.write(response.content)

        deferred_list = []

        urls = ['xxx', 'xxx', 'xxx']

        # create task but not start
        for url in urls:
            deferred = getPage(bytes(url, encoding='utf8')
            deferred.addCallback(get_response)
            deferred_list.append(deferred)


        dlist = defer.DeferredList(deferred_list)
        dlist.addBoth(stop_loop)

        reactor.run()
        ```
- Q:How to achieve Coroutines base on event loop non-blocking
  - Spider just is a socket.
    ```python
    # Custom modules coroutines base on event loop non-blocking.
    import socket
    client = socket.socket()
    client.setblocking(False) # It will be non-blocking when use the function.
    try:
        client.connect(('www.baidu.com', 80)) # Action blocked
    except BlockingIOErrot as e:
        pass


    client = socket.socket()
    client.setblocking(False) # It will be non-blocking when use the function.
    try:
        client.connect(('www.baidu.com', 80)) # Action blocked
    except BlockingIOErrot as e:
        pass


    client = socket.socket()
    client.setblocking(False) # It will be non-blocking when use the function.
    try:
        client.connect(('www.baidu.com', 80)) # Action blocked
    except BlockingIOErrot as e:
        pass

        
    # It can run next step when the client connect successfully.
    client.send(b'GET / http1.1\r\nhost:www.baidu.com\r\n\r\n')

    # It can run next when the thread receive response.
    response = client.recv(8096) # Action blocked
    print(response)

    client.close()
    ```
  - The advantage of non-blocking.
- Q:How to know the status of the socket object?
    ```python
    import socket
    import select

    class Demo(object):

        def __init__(self):
            self.socket_list = []
            self.conn_list = []

        def add_requests(self, url):
            conn = socket.socket()
            conn.setblocking(False)
            try:
                conn.connect((url, 80))
            except BlockingIOError as e:
                pass
            self.socket_list.append(conn)
            self.conn_list.append(conn)
        
        def run(self):
            while True:
                # The arg1 can detect whether the socket received is successful.
                # The arg2 can detect whether the socket status is pass as follows.
                # The arg4 is the timeout of detect time.
                r, w, e = select.select(self.socket_list, self.conn_list, [], 0.05)
                
                for sock in w:
                    sock.send(b'GET / http1.1\r\nhost:www.xxx.com\r\n\r\n')
                    self.conn_list.remove(sock)
                
                for sock in r:
                    data = sock.recv(8096)
                    print(data)
                    sock.close()
                    self.socket_list.reomove(sock)
                
                if not self.socket_list:
                    break
                
    if __name__ == '__main__':
        demo = Demo()
        urls = [
            'www.baidu.com',
            'www.bing.com',
            'www.cnblogs.com'
        ]
        for url in urls:
            demo.add_requests(url)
        demo.run()
    ```
- Q:What is asynchronous?
  - A:It is a function about callback, it will run a func when a task run over.
    > We can meet such as sence:
    > - spider: It will auto run callback func when finish download.
    > - ajax: Running callback func when finish request.
- Q:What is non-blocking?
  - A:It actually is no wait. Socket will be non-blocking when running socket.setblocking(False)
- Q:What the role of IO multiplexing?
  1. Monitor socket status
  2. How to achieve IO multiplexing?
     1. select, It's only monitor 1024 connection; it will loop monitor all socket inside.
     2. poll, It's not limit connection count, but it still loops monitor all sockets inside, only used by linux.
     3. epoll, It's not limit connection count, it uses callback func indside, only used by linux.