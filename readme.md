# 基于RUST的WEB资源服务器

> 该项目于2024年5月1日开始开发

## 预期功能

| 功能                                       | 支持情况 | 当前情况 |
|--------------------------------------------|----------|----------|
| 多线程支持                                  | 是       | 是       |
| 服务支持配置化                              | 是       | 是       |
| 防盗链支持                                  | 是       | 否       |
| gzip支持                                   | 是       | 否       |
| SSL支持                                    | 是       | 否       |
| 反向代理支持                                | 是       | 是       |
| 自定义状态码对应资源文件                     | 是       | 是       |
| 日志支持                                    | 是       | 是       |
| 负载均衡支持                                | 是       | 否       |
| 域名绑定解析支持                             | 是       | 是       |
| 资源解析                                    | 是       | 是       |
| history模式支持                             | 是       | 是       |
| 自定义响应头                                | 是       | 是       |

## 目前进度

* 自定义响应头
* 多线程支持
* 配置化
* 日志输出 
* 资源解析
* history模式支持
* 域名绑定支持
* 一个端口对应多域名支持
* 反向代理支持

## 错误页

* 在root_path下名称为对应状态码.html，例如404对应页面404.html

## JSON示例

> 首次运行会自动生成config.json配置文件，填写好后重新运行即可

```json
{
    "server": [
        {
            "listen_ip": "127.0.0.1",
            "listen_port": 80,
            "buffer_size": 1024,
            "bind_server_name": {
                "127.0.0.1": {
                    "root_path": "./logs",
                    "log_dir_path": "./logs",
                    "ssl_certificate_path": "./ssl/certificate.crt",
                    "ssl_certificate_key_path": "./ssl/certificate.key",
                    "empty_path_try_files_path": "./2024-05-26.log",
                    "response_header": "Server: RUST-WEB-SERVER\r\nGit: https://git.ltpp.vip/root/RUST-WEB-SERVE.git",
                    "proxy": "",
                    "proxy_timeout_seconds": 10
                },
                "localhost": {
                    "root_path": "./",
                    "log_dir_path": "./logs",
                    "ssl_certificate_path": "./ssl/certificate.crt",
                    "ssl_certificate_key_path": "./ssl/certificate.key",
                    "empty_path_try_files_path": "./index.html",
                    "response_header": "Server: RUST-WEB-SERVER\r\nGit: https://git.ltpp.vip/root/RUST-WEB-SERVE.git",
                    "proxy": "http://www.ltpp.vip:60024/visit/add?origin=sqs",
                    "proxy_timeout_seconds": 10
                }
            }
        },
        {
            "listen_ip": "127.0.0.1",
            "listen_port": 81,
            "buffer_size": 1024,
            "bind_server_name": {
                "127.0.0.1": {
                    "root_path": "./",
                    "log_dir_path": "./logs",
                    "ssl_certificate_path": "./ssl/certificate.crt",
                    "ssl_certificate_key_path": "./ssl/certificate.key",
                    "empty_path_try_files_path": "./index.html",
                    "response_header": "Server: RUST-WEB-SERVER\r\nGit: https://git.ltpp.vip/root/RUST-WEB-SERVE.git",
                    "proxy": "http://www.ltpp.vip:60024/visit/add?origin=sqs",
                    "proxy_timeout_seconds": 10
                }
            }
        }
    ]
}
```

> 以上配置将当前目录作为访问地址的根目录，
> 监听了80和81端口，绑定IP和域名为127.0.0.1和localhost用来处理请求，
> 当访问为空则重写路径到当前目录的index.html（适用于Vue等打包后的资源），
> 日志保存在当前目录logs下

> PS:listen_ip为服务端IP, bind_server_name下的key为域名或者IP, 一般listen_ip为127.0.0.1，
> 如果bind_server_name配置了localhost域名，那么可以使用localhost访问，
> 但是不支持127.0.0.1，如需支持127.0.0.1，在bind_server_name中添加即可
> 如果本地做了映射，需要同时添加映射的域名和127.0.0.1
> 配置如下：

```json
"bind_server_name": {
    "localhost": {
        "root_path": "./",
        "log_dir_path": "./logs",
        "ssl_certificate_path": "./ssl/certificate.crt",
        "ssl_certificate_key_path": "./ssl/certificate.key",
        "empty_path_try_files_path": "./index.html",
        "response_header": "Server: RUST-WEB-SERVER\r\nGit: https://git.ltpp.vip/root/RUST-WEB-SERVE.git",
        "proxy": "",
        "proxy_timeout_seconds": 10
    },
    "127.0.0.1": {
        "root_path": "./",
        "log_dir_path": "./logs",
        "ssl_certificate_path": "./ssl/certificate.crt",
        "ssl_certificate_key_path": "./ssl/certificate.key",
        "empty_path_try_files_path": "./index.html",
        "response_header": "Server: RUST-WEB-SERVER\r\nGit: https://git.ltpp.vip/root/RUST-WEB-SERVE.git",
        "proxy": "",
        "proxy_timeout_seconds": 10
    },
    "test.com": {
        "root_path": "./",
        "log_dir_path": "./logs",
        "ssl_certificate_path": "./ssl/certificate.crt",
        "ssl_certificate_key_path": "./ssl/certificate.key",
        "empty_path_try_files_path": "./index.html",
        "response_header": "Server: RUST-WEB-SERVER\r\nGit: https://git.ltpp.vip/root/RUST-WEB-SERVE.git",
        "proxy": "",
        "proxy_timeout_seconds": 10
    }
}
```


## 反向代理

- 系统会检测proxy字段是否是合法URL,如果是合法URL那么系统会进行反向代理,否则会加载静态资源
- 反向代理时,会截取proxy字段除了path部分的url,实际path来自请求时的path,参数会进行拼接,如果proxy参数和请求参数冲突,请求参数会追加在proxy参数后面

## 日志

> 支持配置，日期和完整请求记录

```php
[2024-05-26 13:26:14]
HttpRequest {
    path: "/logs/2024-05-26.log",
    method: "GET",
    query: [],
    headers: {
        "Cookie": "SameSite=Lax; Hm_lvt_9793f42b498361373512340937deb2a0=1689155374; _ga=GA1.1.39230079.1707025003; _ga_69MPZE94D5=GS1.1.1707025002.1.1.1707026740.0.0.0",
        "Upgrade-Insecure-Requests": "1",
        "Sec-Fetch-Site": "none",
        "Host": "127.0.0.1",
        "sec-ch-ua-platform": "\"Windows\"",
        "Accept-Encoding": "gzip, deflate, br, zstd",
        "Sec-Fetch-Dest": "document",
        "Accept-Language": "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
        "Pragma": "no-cache",
        "sec-ch-ua-mobile": "?0",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
        "sec-ch-ua": "\"Microsoft Edge\";v=\"125\", \"Chromium\";v=\"125\", \"Not.A/Brand\";v=\"24\"",
        "Sec-Fetch-Mode": "navigate",
        "Sec-Fetch-User": "?1",
        "Connection": "keep-alive",
        "Cache-Control": "no-cache",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36 Edg/125.0.0.0",
    },
    body: {
        "Cookie": [
            "SameSite=Lax; Hm_lvt_9793f42b498361373512340937deb2a0=1689155374; _ga=GA1.1.39230079.1707025003; _ga_69MPZE94D5=GS1.1.1707025002.1.1.1707026740.0.0.0",
        ],
        "Host": [
            "127.0.0.1",
        ],
        "User-Agent": [
            "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36 Edg/125.0.0.0",
        ],
        "Accept": [
            "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
        ],
        "Sec-Fetch-User": [
            "?1",
        ],
        "Sec-Fetch-Dest": [
            "document",
        ],
        "Upgrade-Insecure-Requests": [
            "1",
        ],
        "Sec-Fetch-Mode": [
            "navigate",
        ],
        "sec-ch-ua-platform": [
            "\"Windows\"",
        ],
        "Connection": [
            "keep-alive",
        ],
        "sec-ch-ua": [
            "\"Microsoft Edge\";v=\"125\", \"Chromium\";v=\"125\", \"Not.A/Brand\";v=\"24\"",
        ],
        "sec-ch-ua-mobile": [
            "?0",
        ],
        "Pragma": [
            "no-cache",
        ],
        "Cache-Control": [
            "no-cache",
        ],
        "Sec-Fetch-Site": [
            "none",
        ],
        "Accept-Language": [
            "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
        ],
        "Accept-Encoding": [
            "gzip, deflate, br, zstd",
        ],
    },
}

[2024-05-26 13:26:14]
"Resource load success:./logs/2024-05-26.log"


[2024-05-26 13:26:14]
"Resource load success:./logs/2024-05-26.log"


[2024-05-26 13:26:16]
HttpRequest {
    path: "/_static/out/browser/serviceWorker.js",
    method: "GET",
    query: [],
    headers: {
        "Referer": "http://127.0.0.1/_static/out/browser/serviceWorker.js",
        "Sec-Fetch-Site": "same-origin",
        "Accept-Language": "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
        "Sec-Fetch-Mode": "same-origin",
        "Sec-Fetch-Dest": "serviceworker",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36 Edg/125.0.0.0",
        "Service-Worker": "script",
        "Cache-Control": "max-age=0",
        "Accept-Encoding": "gzip, deflate, br, zstd",
        "Connection": "keep-alive",
        "Cookie": "SameSite=Lax; Hm_lvt_9793f42b498361373512340937deb2a0=1689155374; _ga=GA1.1.39230079.1707025003; _ga_69MPZE94D5=GS1.1.1707025002.1.1.1707026740.0.0.0",
        "Host": "127.0.0.1",
        "Accept": "*/*",
    },
    body: {
        "Accept-Language": [
            "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
        ],
        "Host": [
            "127.0.0.1",
        ],
        "Cookie": [
            "SameSite=Lax; Hm_lvt_9793f42b498361373512340937deb2a0=1689155374; _ga=GA1.1.39230079.1707025003; _ga_69MPZE94D5=GS1.1.1707025002.1.1.1707026740.0.0.0",
        ],
        "User-Agent": [
            "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36 Edg/125.0.0.0",
        ],
        "Sec-Fetch-Mode": [
            "same-origin",
        ],
        "Sec-Fetch-Site": [
            "same-origin",
        ],
        "Connection": [
            "keep-alive",
        ],
        "Accept": [
            "*/*",
        ],
        "Cache-Control": [
            "max-age=0",
        ],
        "Service-Worker": [
            "script",
        ],
        "Referer": [
            "http://127.0.0.1/_static/out/browser/serviceWorker.js",
        ],
        "Sec-Fetch-Dest": [
            "serviceworker",
        ],
        "Accept-Encoding": [
            "gzip, deflate, br, zstd",
        ],
    },
}

[2024-05-26 13:26:16]
"Resource load success:./logs/2024-05-26.log"


[2024-05-26 13:26:16]
"Resource load success:./logs/2024-05-26.log"


[2024-05-26 13:26:32]
HttpRequest {
    path: "/visit/add",
    method: "GET",
    query: [
        (
            "origin",
            "localhost",
        ),
    ],
    headers: {
        "Upgrade-Insecure-Requests": "1",
        "Accept-Encoding": "gzip, deflate, br, zstd",
        "sec-ch-ua-mobile": "?0",
        "Sec-Fetch-Dest": "document",
        "Cache-Control": "no-cache",
        "Pragma": "no-cache",
        "sec-ch-ua": "\"Microsoft Edge\";v=\"125\", \"Chromium\";v=\"125\", \"Not.A/Brand\";v=\"24\"",
        "sec-ch-ua-platform": "\"Windows\"",
        "Sec-Fetch-Mode": "navigate",
        "Sec-Fetch-User": "?1",
        "Cookie": "SameSite=Lax; Hm_lvt_9793f42b498361373512340937deb2a0=1689155374; _ga=GA1.1.39230079.1707025003; _ga_69MPZE94D5=GS1.1.1707025002.1.1.1707026740.0.0.0",
        "Sec-Fetch-Site": "none",
        "Accept-Language": "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36 Edg/125.0.0.0",
        "Host": "127.0.0.1:81",
        "Connection": "keep-alive",
    },
    body: {
        "Accept-Language": [
            "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
        ],
        "Sec-Fetch-Site": [
            "none",
        ],
        "Upgrade-Insecure-Requests": [
            "1",
        ],
        "sec-ch-ua": [
            "\"Microsoft Edge\";v=\"125\", \"Chromium\";v=\"125\", \"Not.A/Brand\";v=\"24\"",
        ],
        "sec-ch-ua-platform": [
            "\"Windows\"",
        ],
        "Sec-Fetch-Mode": [
            "navigate",
        ],
        "Sec-Fetch-Dest": [
            "document",
        ],
        "Cookie": [
            "SameSite=Lax; Hm_lvt_9793f42b498361373512340937deb2a0=1689155374; _ga=GA1.1.39230079.1707025003; _ga_69MPZE94D5=GS1.1.1707025002.1.1.1707026740.0.0.0",
        ],
        "Accept": [
            "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
        ],
        "Pragma": [
            "no-cache",
        ],
        "Connection": [
            "keep-alive",
        ],
        "sec-ch-ua-mobile": [
            "?0",
        ],
        "Cache-Control": [
            "no-cache",
        ],
        "User-Agent": [
            "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36 Edg/125.0.0.0",
        ],
        "Sec-Fetch-User": [
            "?1",
        ],
        "Accept-Encoding": [
            "gzip, deflate, br, zstd",
        ],
        "Host": [
            "127.0.0.1:81",
        ],
    },
}

[2024-05-26 13:26:32]
"Proxy url info:\nhttp://www.ltpp.vip:60024/visit/add?origin=sqs&origin=localhost"


[2024-05-26 13:26:32]
"Proxy success:\n{\"code\":1,\"msg\":\"success\"}"



```

## 实际效果
![](markdown-images/image-1.png)

### 控制台输出
![](markdown-images/image.png)

### 404
![](markdown-images/image-2.png)