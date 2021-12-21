* Kintohub已经封了这个项目，所以只能部署到Heroku了，下面是部署到Heroku的方法。
* Heroku也封了这个项目，可以fork本项目，修改项目名和部署链接，即可正常部署。

部署链接格式如下，等号后面改成你fork过去的项目链接：
`https://dashboard.heroku.com/new?template=https://github.com/yeahwu/kinto`

> 提醒： 滥用可能导致账户被BAN！！！  

Heroku上部署v2ray，可更改传输协议vmess和vless，默认是vmess传输，部署时在v2ray protocol选项框改成你喜欢的协议即可．

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://dashboard.heroku.com/new?template=https:/aizuAllen7428/github.com/yeahwu/soho)

#### 部署服务端

点击上面紫色`Deploy to Heroku`，会跳转到heroku app创建页面，填上app的名字，选择你喜欢的传输协议vmess或者vless，最后换上从 https://111111.online/getuuid.html 拷贝过来的UUID，点击下面deploy创建APP，完成后会生成一个链接，点击链接显示“Bad Request”就说明部署成功了！

需要记下的是appname,和你填入的UUID，下面就可以设置客户端翻墙了。

#### 客户端设置

注意和服务端协议保持一致，选择vmess或者vless，其它照图填写：

![v2ray](/img/kinto3.jpg)

上图是443端口设置方法，当然你也可以设置80端口，不要打开tls就可以了，如下：

```
地址(address) : appname.herokuapp.com	//appname替换成你的app名字

端口(port) : 80

用户ID(uuid) : c95ef1d4-f3ac-4586-96e9-234a37dda068

加密方式(security) : aes-128-gcm

传输协议(network) : ws
```

本地客户端config.json文件配置：
```
{
  "inbounds": [
    {
      "port": 1080,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth",
        "udp": false
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",  //此处选择vmess或者vless
      "settings": {
        "vnext": [
          {
            "address": "appname.herokuapp.com",  //域名
            "port": 443,
            "users": [
              {
                "id": "c95ef1d4-f3ac-4586-96e9-234a37dda068"
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {}
      }
    }
  ]
}
```

本地Clash客户端config.yaml文件配置：

```
  - name: "HEROKU"
    type: vmess
    server: appname.herokuapp.com
    port: 443
    uuid: c95ef1d4-f3ac-4586-96e9-234a37dda068
    alterId: 0
    cipher: aes-128-gcm
    udp: true
    tls: true
    #skip-cert-verify: true
    servername: appname.herokuapp.com
    network: ws
```

#### Workers反代

```
addEventListener(
    "fetch",event => {
        let url=new URL(event.request.url);
        url.hostname="kinto-404.us1.kinto.io";
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```
