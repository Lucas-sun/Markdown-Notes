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