1. **使用github提交的时候，出现报错Support for password authentication was removed on August 13**

   更换电脑,需要重新设置 token

```
大概意思就是你原先的路码先证从2021年8月13日开始就不能用了，
必须使用个人访问令牌(personal accoss token)，就是把你的 密码 替换成 token,
在git push 代码的时候 会自动弹出写 usename  以及密码 密码换成 token 就好


账号: woniuBaBy
密码: 以后再复制吧  设置有效期 暂时为一年 ()
```

git网站

重新设置 token  自己百度吧.无法上传



2. 设置代理相关

   ```
   Git 代理设 --global http.proxy 'http://127.0.0.1:1087'
   git config --global https.proxy 'http://127.0.0.1:1087'
   取消代理
   git config --global --unset http.proxy
   git config --global --unset httpx.proxy
   
   [http]
           proxy = socks5://127.0.0.1:7891
   [https]
           proxy = socks5://127.0.0.1:7891
   
   [http "https://raw.githubusercontent.com"]
   	proxy = socks5://127.0.0.1:1087
   [http "https://chromium.googlesource.com"]
   	proxy = socks5://127.0.0.1:1087
   [http "https://webrtc.googlesource.com"]
   	proxy = socks5://127.0.0.1:1087
   [http "https://github.com"]
   	proxy = socks5://127.0.0.1:1087
   [http "http://github.com"]
   	proxy = socks5://127.0.0.1:1087
   [http "https://chrome-infra-packages.appspot.com"]
   	proxy = socks5://127.0.0.1:1087
   
   [http]
   	postBuffer = 524288000
   	lowSpeedLimit = 0
   	lowSpeedTime = 999999
   	sslVerify = false
      	 proxy = http://127.0.0.1:1087
     	  version = HTTP/1.1
   [https]
       proxy = http://127.0.0.1:1087
   
   
   socks5://127.0.0.1:1088
   
   [http]
   	sslVerify = false
   	proxy =  socks5://127.0.0.1:1088
     	version = HTTP/1.1
   [https]
   	proxy = socks5://127.0.0.1:1088
   ```

3. 终端也VPN

   ```
   //终端复制,并进行终端也vpn
   bash配置：
   
   1.修改全局配置
   vim ~/.bash_profile
   2.使配置生效
   source ~/.bash_profile
   # curl ipinfo.io
   {
     "ip": "114.110.1.38",
     "hostname": "No Hostname",
     "city": "Beijing",
     "region": "Beijing Shi",
     "country": "CN",
     "loc": "39.9289,116.3883",
     "org": "AS4808 CNCGROUP IP network China169 Beijing Province Network"
   }%
   
   # curl https://ip.cn
   当前 IP: 120.133.6.22 来自: 天津市 第一线
   
   # curl cip.cc
   IP  : 114.110.1.38
   地址  : 中国  北京市
   数据二 : 北京市 | 广东恒敦通信技术北京分公司
   URL : http://www.cip.cc/114.110.1.38
   
   # curl myip.ipip.net
   当前 IP：114.110.1.38  来自于：中国 北京 北京 联通/电信
   
   # curl ifconfig.me
   114.110.1.38
   
   # curl http://members.3322.org/dyndns/getip
   114.110.1.38
   
   //终端测试是否成功
   curl https://www.google.com
   
   
   proxy_on就会启动。
   proxy_off就会关闭。
   ```

   

4.记录

```
在命令行中输入一下命令，添加临时环境量
export http_proxy=socks5://127.0.0.1:1080
export https_proxy=socks5://127.0.0.1:1080

设置git环境变量
git config –global http.proxy 'socks5://127.0.0.1:1080'

//全局统一代理
export ALL_PROXY=http://127.0.0.1:1087

//查看
export -p

//git 账号列表 
git config --list
修改 名字以及 邮箱
git config --global user.name "yanjinchao"
git config --global user.email "yanjinchao@xiaoyouzi.com"

git config --global user.password ""

```

