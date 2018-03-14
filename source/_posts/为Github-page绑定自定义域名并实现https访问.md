---
title: 为Github page绑定自定义域名并实现https访问
tags:
  - github
  - SSL
  - CDN
categories:
  - Configuration
date: 2018-03-14 20:28:08
---

<Excerpt in index | 首页摘要> 
`实现目标`
- 获取自定义域名`yucichueng.me`;
- 将上述域名(及**www**域名)解析到`yucichueng.github.io`IP地址;
- 将域名解析服务托管于**CloudFlare**;
- 获取**SSL**证书，使网站可以通过**https**访问。
<!-- more -->
<The rest of contents | 余下全文>
## 获取自定义域名
我购买的域名是`yucicheung.me`。
一级域名`me`在[GoDaddy](https://au.godaddy.com/)网站进行购买。心仪的域名可以在网站[whois](https://www.whois.net/)查看是否被注册，未被注册可在相应域名商处购买。
**Tips:**如果是在`Godaddy`购买的域名，建议购买隐私服务(不泄露个人信息)，不是很便宜，所以建议可以直接从国内域名商处购买，赠送隐私保护服务。
## 域名解析到Github page
购买域名后，在对应域名供应商处一般会提供域名解析服务，我们需要对解析服务进行配置。
对我来说，就要在`Godaddy`域名解析服务处进行配置。
### 修改域名服务商解析记录
1. 点击主页右上角自己的账户名-->点击**manage my domains**-->在**My Domains**选择**Manage DNS**;
<div align=center>
![manage_DNS](\img\manage_DNS.png)
</div>
2. 添加`CNAME`和`A`解析记录，使`www`网址和`@`(即本身)都指向自己的github page；
按以下表格分别添加**A**和**CNAME**两条解析记录：

| 类型type | 主机host | 指向points to | TTL(Time To Live) |
| :---: | :---: | :---: | :---: |
| `A` | `@` | github page的IP 地址| 600 |
| `CNAME` | `www` | github page的网址| 600 |
| **A记录**：<br>用来指定域名的IPv4地址<br>如要将域名指向一个IP地址<br>添加A记录<br><br>**CNAME**：<br> 如要将域名指向另一域名<br>再由该域名提供ip<br>添加CNAME记录|**www**：<br>解析后域名为**www.yucicheung.me**<br><br>**@**：<br>解析主域名**yucicheung.me**<br><br>**\***：<br>泛解析，匹配其他所有域名，**\*.yucicheung.me**|对于`A记录`:<br>要指向IP地址<br><br>对于`CNAME`:<br>要指向一个域名<br><br>|指地方dns缓存域名记录的时间,缓存失效后会再次获取<br><br>**600**:<br>建议采用600<br><br>**60**:<br>如果经常修改IP可用(修改可快速生效),长期使用略影响解析速度<br><br>**3600**:<br>如果IP极少变动(一年几次),可选择 3600,解析速度快|

**Tips**：github page的IP地址可以通过以下命令获取：
```bash
ping yucicheung.github.io
```

### 添加github仓库的域名解析记录
1. 按以下命令在自己的github仓库中添加CNAME文件，其中填入购买的域名。或者可以在仓库的**settings**中设置**Custom Domain**设置好自己的域名，github会自动添加CNAME文件。
```bash
$ touch CNAME
$ echo 'yucicheung.me'> CNAME 
```
2. 稍等一下就可以通过购买域名**yucicheung.me**访问**yucicheung.github.io**了，但是这时只能通过`http`访问自定义域名而非`https`。

## 通过HTTPS访问自定义域名
在完成上述操作以后，只能通过`HTTP`协议传输(明文传输),于是在通过自定义域名访问自己的github page时，发现浏览器提示**该网址不安全，没有合格的安全证书**，不能通过`https`(密文传输)访问。
### HTTP与HTTPS
**HTTP**是明文传输协议，传输内容容易被嗅探和篡改。
而**HTTPS**，即**HTTP over SSL/TLS**,是添加了一层**SSL(Secure Sockets Layer，安全套接层)**，或者是**TLS(Transport Layer Security,传输层安全协议)**，所以**HTTPS**就可以视为**HTTP**和**SSL/TLS**协议的组合。
<br>
**HTTPS**能做到良好的保密性(防嗅探)，真实性(防篡改)，完整性(防域名劫持和域名欺骗)。
### SSL证书
**SSL**是**TLS**的前身，但**TLS**通常也被标志为**SSL**。
**SSL/TLS**协议的基本思路是采用公钥加密法，也就是说，客户端先向服务器端索要公钥，然后用公钥加密信息(会话秘钥)，服务器收到密文后，用自己的私钥解密。
这个公钥就放在数字证书中。只要证书是可信的，公钥就是可信的。
### 申请SSL证书
SSL证书由你的NS(Name Server，域名服务商)颁发，由于`GoDaddy`的SSL证书超级贵，所以我们可以迁移到免费提供SSL的NS处，比如国内的**DNSpod**(国内都需要备案),还有国外的**Netlify**和**Cloudflare**，从速度和操作性考虑，本人选择了`Cloudflare`。
1. 到[Cloudflare官网](https://www.cloudflare.com/)注册；
2. 根据指引点击**Add Site**，添加自定义域名**yucicheung.me**，会自动开始扫描DNS解析记录；
3. 扫描完成后，**Cloudflare**会选择给我们分配两个NS地址，将这两个地址替换**GoDaddy**上的原NS地址，等待生效；
<div align=center>
![change_ns](/img/change_ns.png)
</div>
4. 在**Cloudflare**上检查自己网站的状态，显示为`Active`时表示NS更改成功；
<div align=center>
![active](/img/Active.png)
</div>
5. 在**Cloudflare**将自己网站的**SSL**状态改变为`Full`状态，等待**Status**变为`Active Certificate`，通常生效需要十几分钟。
<div align=center>
![ssl](/img/full_SSL.png)
</div>
6. 再访问自定义域名时，就可以看见是`https`传输，网址前也有一把绿色小锁,可以看到这个证书其实是Cloudflare的证书。
<div align=center>
![https](/img/https.png)
![ssl](/img/ssl.png)
</div>
## CDN:关于访问速度
如果因为NS在国外，担心访问速度，可以稍微放心。
**Cloudflare**本身提供`CDN`(content delivery network,内容分发网络)服务，是指一种通过互联网互相连接的电脑网络系统，利用最靠近每位用户的服务器，就近获取网络内容传递给用户。
免费服务一般只能加速**css、js**的引用，而对于**html**和**图片**的加速通常是付费服务。
实验过之后，确实是图片加载会稍慢一些，但是整体速度是可以接受的，**如果实在介意速度，建议通过国内NS解析网址**。
另：发现Cloudflare已经和baidu合作针对企业级用户启动加速服务，一个节点服务海外访问者，另一个服务国内访问者。
## 总结
- 最后实现的整个系统可以这么理解：就相当于由github提供主机（相当于寄存在github的服务器），自己购买域名，域名指向服务器文件进行展示。
- 原本的github主页，域名解析由GitHub负责，SSL证书由github.com提供，所以可以安全访问。现在的域名解析由Cloudflare服务，SSL证书也由其提供。
