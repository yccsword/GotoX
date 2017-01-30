# GotoX
- GotoX 修改自 goagent，可用于访问被**防火墙**屏蔽的网络服务。
- 其特色，一是自动代理，可支持标准 HTTP/1.1 请求；二是可根据需要修改来自客户端的请求以及服务器的响应。
- 主要使用 GAE 服务作为后端代理，也支持任意 HTTP/SOCKS4/SOCKS5 代理，两者处于同等地位。SOCKS 代理支持认证。
- 运行时会一直维护一个较小但快速的 GAE IP 列表。

# 部署
- 配置 GAE 路径为“***/_gh/*** ”可使用 GoProxy、XX-Net 及 GoAgent 3.2.X 服务端，“***/2***  ”使用 GoAgent 3.1.X 服务端。
- 推荐使用 [GoProxy 服务端](https://github.com/phuslu/goproxy/tree/server.gae)。
- 部署服务端时，请在 VPN、Shadowsocks 等代理条件下上传，或者去 [XX-net 配置](https://github.com/XX-net/XX-Net/blob/master/code/default/gae_proxy/local/proxy.ini) 中取用公共 APPID 填入本代理来上传。公共 APPID **不支持视频和下载**。
    - **相关链接**
    - 简易教程 https://github.com/phuslu/goproxy/blob/wiki/SimpleGuide.md
    - 常见问题 https://github.com/phuslu/goproxy/blob/wiki/FAQ.md
    - 新版谷歌云部署问题 https://github.com/XX-net/XX-Net/issues/4720
    - GoProxy 服务端 https://github.com/phuslu/goproxy/tree/server.gae
    - XX-Net 服务端 https://github.com/jzp820927/Deploy_XXNET_Server
    - XX-net 公共 APPID https://github.com/XX-net/XX-Net/blob/master/code/default/gae_proxy/local/proxy.ini

# 使用
- 具体配置说明，在配置文件中都有较为详细的描述。只有 **Config.ini** 支持 **Config.user.ini** 用户配置。
- 需事先提供由**其它扫描工具**取得一个较大的（**上万也支持**）**可用** GAE IP 列表以供筛选，放入“**data/ip.txt**”或“**data/ipex.txt**”（优先）中，格式为每行一个完整 IP。
- 也可以在**［GAE/iplist］**配置中指定使用固定的 GAE IP 列表，不再进行 IP 检查筛选。
- 自动代理规则和 IP 列表文件可以在运行时替换，无需重启 GotoX。
-  google IP 列表名称“**google_gws、google_com**”选项由代理自身维护，无需用户填写；用户可以使用其它名称配置自己的 IP 列表，以供自动规则使用。
    - “**google_gws**”是未分类的 gws 服务器，用于 GAE 代理，一般不用设置成这个。
    - “**google_com**”是支持一般谷歌域名反向代理的服务器，可用于大部分 google 域名直连，转发则有极小概率出现证书错误。
    - 默认谷歌配置是用 google_gws 直连，兼容性较好；也可配置成用 google_com 转发，保持谷歌证书不变，特殊谷歌主机名需自行测试调整。
- 现提供两个端口。
    - 自动代理端口需自行配置规则，可根据需要自动分配链接路径，推荐使用（开发动力之一）；
    - 要使用自动代理请先仔细阅读配置规则说明，由于未添加完全的检测，错误规则可能导致程序出错或非预期的代理结果；
    - GAE 端口完全使用 GAE 代理，只有当遇到不支持的方法时转用直连，如果此网络资源处于屏蔽状态链接会失败。
- 成功运行后会创建独一无二的 CA 证书，证书名称为：“**GotoX CA**”。配置好浏览器代理后，在地址栏输入“**http://gotox.go/**” 即可安装或下载 CA 证书，也可在“**cert**”文件夹找到“**CA.crt**”证书文件。由于还不完善，暂时不打算启用自动导入和删除功能，如有需求请手动删除老旧证书。
    - **相关链接**
    - 手动导入证书 https://github.com/XX-net/XX-Net/wiki/GoAgent-Import-CA
- **小技巧：**
    - 对于不支持 GAE 出口的网址
        - 可在**转发（forward）或直连（direct）**规则中设置成**反向代理** IP；
        - 或在**其它代理（proxy）**规则中设置成 **SOCKS 代理**（格式见 ActionFilter.ini）。
    - 反向代理一般**不支持**非加密链接，请**慎用**支持非加密链接的反向代理！

# 兼容性
- CPython 3.4/3.5 已测试。
- **不支持** Python 2，保持兼容比较麻烦，已放弃。
- 必须组件（可放入“**python/site-packages**”目录，支持 “**.egg**”）：
    - gevent 1.1.2 及以上
    - pyOpenSSL 16.0.0 及以上
    - dnslib 0.8.3 及以上
    - PySocks
- 发布将提供包含 Windows CPython 3.5 环境的便携版本。
- IPv6 未测试，欢迎反馈。
- 由于自己只使用 Windows，所以其它系统不保证能正常使用。如果有需求作者会尽量修改，但这需要有人帮助测试反馈。

# 计划
- 由于近期较为忙碌，以下计划不确定完成时间。
- 请求和响应的修改还未完成，将会加入。
- 非 GAE 链接的自动多线程支持（低）。
- 前置代理代码还未整理，暂时不提供此功能。请使用转发到后端代理（不通过 GAE）。
- 不会提供对 HTTP/2 的直接支持，对我个人来说带来的改善无法与付出对等，代码不是我的本行。其部分特性可用多 IP、多 appid 以及 keep-alive 的组合来替代，主要损失的是头部的压缩支持，其次某些情况下延迟更大些，尤其是对应 IP 稀少或只有 1 个 IP 时。**仅在服务器支持 HTTP/2 时对比。**
    - **GAE** 问题不大，实际的头部也会压缩。
    - **直接转发**完全等于直连，是否支持看客户端。
    - **转发代理**还要看代理服务器是否支持。
    - 其余的就完全不支持了。
