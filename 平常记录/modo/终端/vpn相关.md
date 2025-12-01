* 代理相关

Git 代理设 --global http.proxy 'http://127.0.0.1:1087'

git config --global https.proxy 'http://127.0.0.1:1087'

取消代理

git config --global --unset http.proxy

git config --global --unset httpx.proxy



[http]

​    proxy = socks5://127.0.0.1:7891

[https]

​    proxy = socks5://127.0.0.1:7891



[http "https://raw.githubusercontent.com"]

​	proxy = socks5://127.0.0.1:1087

[http "https://chromium.googlesource.com"]

​	proxy = socks5://127.0.0.1:1087

[http "https://webrtc.googlesource.com"]

​	proxy = socks5://127.0.0.1:1087

[http "https://github.com"]

​	proxy = socks5://127.0.0.1:1087

[http "http://github.com"]

​	proxy = socks5://127.0.0.1:1087

[http "https://chrome-infra-packages.appspot.com"]

​	proxy = socks5://127.0.0.1:1087





[http]

​	postBuffer = 524288000

​	lowSpeedLimit = 0

​	lowSpeedTime = 999999

​	sslVerify = false

   proxy = http://127.0.0.1:1087

 	 version = HTTP/1.1

[https]

  proxy = http://127.0.0.1:1087





socks5://127.0.0.1:1088



[http]

​	sslVerify = false

​	proxy = socks5://127.0.0.1:1088

 	version = HTTP/1.1

[https]

​	proxy = socks5://127.0.0.1:1088



```objective-c
//终端复制,并进行终端也vpn

- bash配置：

1.修改全局配置

vim ~/.bash_profile

2.使配置生效

source ~/.bash_profile

\# curl ipinfo.io

{

 "ip": "114.110.1.38",

 "hostname": "No Hostname",

 "city": "Beijing",

 "region": "Beijing Shi",

 "country": "CN",

 "loc": "39.9289,116.3883",

 "org": "AS4808 CNCGROUP IP network China169 Beijing Province Network"

}%



\# curl https://ip.cn

当前 IP: 120.133.6.22 来自: 天津市 第一线



\# curl cip.cc

IP : 114.110.1.38

地址 : 中国 北京市

数据二 : 北京市 | 广东恒敦通信技术北京分公司

URL : http://www.cip.cc/114.110.1.38



\# curl myip.ipip.net

当前 IP：114.110.1.38 来自于：中国 北京 北京 联通/电信



\# curl ifconfig.me

114.110.1.38



\# curl http://members.3322.org/dyndns/getip

114.110.1.38



//终端测试是否成功

curl https://www.google.com
```



>  vim ~/.bash_profile 在此文件中可查看 (已经配好)

proxy_on就会启动。

proxy_off就会关闭。



* //全局统一代理

export ALL_PROXY=http://127.0.0.1:1087