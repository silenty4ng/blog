---
title: DoH 服务器的 Python 实现
categories: 散文杂记
tags: 
    - python
    - doh
date: 2023/12/26 22:25
updated: 2023/12/26 22:25
---

## 实现
看到有 Go、PHP 语言实现的 DoH 服务器，于是弄了个 Python 实现的 DoH 服务器（兼容 RFC8484），可以使用 Nginx、Caddy 反向代理 `/dns-query` 接口完成部署。

```
"""
/*
 * @Author: Silent YANG 
 * @Date: 2023-11-29
 */
"""

from flask import Flask, request, make_response
from base64 import urlsafe_b64decode
import socket

app = Flask(__name__)
socket.setdefaulttimeout(5)

@app.route("/dns-query" ,methods=['GET', 'POST'])
def dns_query():
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    if request.headers.get('CONTENT_TYPE') == 'application/dns-message':
        sock.sendto(request.stream.read(), ("127.0.0.1", 53))
        rx_meesage, addr = sock.recvfrom(4096)
        return make_response(rx_meesage, 200, {"Content-Type": "application/dns-message"})
    if request.method == "GET" and request.args.get("dns"):
        payload = urlsafe_b64decode(request.args.get("dns") + "===")
        sock.sendto(payload, ("127.0.0.1", 53))
        rx_meesage, addr = sock.recvfrom(4096)
        return make_response(rx_meesage, 200, {"Content-Type": "application/dns-message"})
    sock.close()
    return make_response("", 400)

if __name__ == "__main__":
    app.run('127.0.0.1', debug=False, port=5000)
```

## 完整代码

[https://github.com/silenty4ng/DoH](https://github.com/silenty4ng/DoH)
