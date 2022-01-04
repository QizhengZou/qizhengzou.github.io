---
title: "ISC_base_04"
date: 2021-11-21T11:35:58+08:00
lastmod: 2021-12-07
tags: [information_security_conspectus]
categories: [School Courses]
slug: identity authentication
draft: false
---
# 身份认证
## 4.1 概述	
- 问题的提出：什么是身份认证？
    - 身份认证是证实用户的真实身份与其所声称的身份是否相符的过程。
- 身份认证的依据应包含只有该用户所特有的、并可以验证的特定信息。
    - 用户所知道的或所掌握的信息（Something the user know），如密码、口令等；
    - 用户所拥有的特定东西（Something the user possesses），如身份证、护照、密钥盘等；
    - 用户所具有的个人特征（Something the user is or How he behaves），如指纹、笔迹、声纹、虹膜、DNA等。
- 目前身份认证技术主要包括三类：
    - 基于口令的认证技术： 简单灵活，但容易泄露。
    - 基于密码学的认证技术：包括基于对称密钥的认证、基于公开密钥的认证协议。
    - 生物特征的认证技术：依附于人体，不易伪造，不易模仿！
- 身份认证
    - 根据认证条件的数目分类：
        - 仅通过一个条件的相符合来证明一个人的身份，称之为单因子认证；
        - 通过两种不同条件来证明一个人的身份，称之为双因子认证；
        - 通过组合多种不同条件来证明一个人的身份，称之为多因子认证。
    - 根据认证数据的状态来看：
        - 静态数据认证：指用于识别用户身份的认证数据事先已产生并保存在特定的存储介质上；
        - 动态数据认证：指用于识别用户身份的认证数据不断动态变化，每次认证使用不同的认证数据，即动态密码。动态密码由一种称为动态令牌的专用设备（硬件或软件）产生，其产生动态密码的算法与认证服务器采用的算法相同。
## 4.2 认证协议
认证协议：
- 以网络为背景的认证技术的核心基础是密码学。
    - 对称密码和公开密码是实现用户身份识别的主要技术。
- 实现认证必须要求示证方和验证方遵循一个特定的规则来实施认证，这个规则被称为认证协议。
- 认证过程的安全取决于认证协议的完整性和健壮性。
### 4.2.1 基于对称密钥的认证协议
基于对称密钥的认证协议：
- 示证方和验证方共享密钥，通过共享密钥来维系彼此的信任关系，实际上认证就是建立某种信任关系的过程。
- 在只有少量用户的封闭式网络系统中，各用户之间的双人共享密钥的数量有限，可以采用挑战-应答方式来实现认证；
- 对于规模较大的网络系统，一般采用密钥服务器的方式来实现认证，即依靠可信的第三方完成认证。
- 有关符号表示：
    - 𝐀→𝐁：表示A向B发送一条信息。
    - 𝑬_k (𝒙)：使用共享秘钥k对信息串x加密。
    - 𝒙||𝒚：表示x和y相连接。
基于挑战-应答方式的认证协议
- 由验证方生成一个大的随机数据串，即挑战，将挑战发送给示证方。
- 示证方使用共享秘钥加密挑战，然后回送给验证方，
- 验证方通过解密密文得到挑战，通过验证挑战的正确与否，来认证示证方的身份。

Needham-Schroeder认证协议：
- 所有的使用者共同信任一个公正的第三方，此第三方被称为认证服务。
- 每个使用者需要在认证服务器AS（Authentication Server）上完成注册，AS保存每一个用户的信息并与每一个用户共享一个对称密钥。
- 用户和AS之间的信任关系依靠它们的共享秘钥来维系。
- 有关符号：
    - KDC （Key Distribution Center）为AS的秘钥分配中心，主要功能是为用户生成、分发通信秘钥。
    - 𝐈𝐃_𝐀 和𝐈𝐃_𝐁分别是A和B的网络用户标识。

Needham-Schroeder协议描述:
- ……

Needham-Schroeder协议的漏洞：
- Needham-Schroeder协议存在漏洞。
- 假定攻击方C掌握了A和B之间通信的一个老的会话。
    - C可以在第3步冒充A利用老的会话欺骗B。
    - 除非B记住所有以前使用的与A通信的会话密钥，否则B无法判断这是一个重放攻击。

Kerberos
- Kerberos的设计目标是用对称密钥系统为客户机/服务器应用程序提供强大的第三方认证服务。
    - 每个用户或应用服务器与Kerberos分享一个对称密钥。
    - Kerberos由两个部分组成：
        - 认证服务器AS（Authentication Server）
        - 票据授予服务器TGS（Ticket Granting Server）。
    - 允许一个用户通过交换加密消息，在整个网络上与另一个用户或应用服务器互相证明身份，Kerberos给通讯双方提供对称密钥。
    - 票据Ticket是客户端访问服务器时，提交的用于证明自己身份，并可传递通信会话秘钥的认证资料。
        - AS负责签发访问TGS服务器的票据，TGS负责签发访问其它应用服务器的票据。
- 协议内容
    - ……

Windows用户登录认证过程：
- ……

### 4.2.2 **基于公开密钥的认证协议**
基于公开密钥的认证协议：
- 基于公开密钥体制下的认证协议通常有两种认证方式：
    - 方式一：实体A需要认证实体B，A发送一个明文挑战消息（也称挑战因子，通常是随机数）给B，B接收到挑战后，用自己的私钥对挑战明文消息加密，称为签名；B将签名信息发送给A，A使用B的公钥来解密签名消息，称为验证签名，以此来确定B是否具有合法身份。
    - 方式二：实体A将挑战因子用实体B的公钥加密后发送给B，B收到后是用自己的私钥解密还原出挑战因子，并将挑战因子明文发还给A，A可以根据挑战因子内容的真伪来核实B的身份。

Needham-Schroeder公钥认证：
- ……

**基于CA数字证书的认证协议**：
- 基于CA数字证书的认证协议属于公开秘钥的认证协议范畴，只是引入了一个可信的第三方来管理公钥并提供仲裁。在实际的网络环境中，公钥是采用数字证书（Certificate）的形式来完成发布的。
- 数字证书是一个经过权威的、可信赖的、公正的第三方机构（即CA认证中心，Certificate Authority）签名的包含拥有者信息及公开密钥的文件。
- X. 509 V3证书格式：
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211207091217.png)

基于数字证书进行身份认证的过程：
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211207091311.png)
- 通过5个环节，B可以确认A的身份及其签名的信息。
    - A提交资料，申请证书。
    - CA审核A的资料，颁发用CA私钥签过名的数字证书。
        - 该数字证书包含了A的身份信息和A的公钥。由于使用了CA的私钥签名，因此其他人无法伪造。
    - A使用私钥对特定信息进行签名，连同数字证书一起发送给B，B为验证方。
    - B为了能够核实A的数字证书的真伪，必须先获得CA的公钥。
    - B使用CA的公钥对A的数字证书进行合法性验证，通过后获得A的公钥，对A签过名的特定信息进行认证，进而确认A的身份及其签名的信息。
## 4.3 公钥基础设施PKI	
公钥基础设施PKI：
- 为了解决Internet上电子商务等应用的安全问题，世界各国经过多年的研究，初步形成了一套完整的Internet安全解决方案，即目前被广泛采用的公钥基础设施（Public Key Infrastructure，PKI）。
- PKI是一种遵循一定标准的密钥管理基础平台，为所有网络应用提供加密和数字签名等密码服务所必需的密钥和证书管理。
    - PKI就是利用公钥理论和技术建立的提供安全服务的基础设施。   
    - 用户可利用PKI平台提供的服务进行安全的电子交易、通信和互联网上的各种活动。
### 4.3.1 PKI体系结构	
PKI采用数字证书技术来管理公钥，通过CA认证中心把用户的公钥和用户的其他标识信息捆绑在一起，在互联网上验证用户的身份。中心是公钥算法和数字证书技术。CA认证中心是第三方的可信任机构。

PKI体系结构：
- 认证机构CA是PKI的核心执行机构，也称为认证中心，其主要功能包括数字证书的申请注册、证书签发和管理。
    - 其工作内容包括验证并标识证书申请者的身份，对证书申请者的信用度、申请证书的目的、身份的真实可靠性等问题进行审查，确保证书与身份绑定的正确性。
    - 当服务范围较大时，CA可以拆分出证书申请注册机构（Registration Authority，RA），专门负责证书的注册申请和撤销申请等管理工作。
- 证书库（Repository）是CA颁发证书和撤销证书的集中存放地，是网上的公共信息库，可供公众进行开放式查询。
    - 查询的目的有两个：一是想得到与之通信实体的公钥，二是要验证通信对方的证书是否已进入黑名单。
    - 证书库一般采用LDAP（Lightweight Directory Access Protocol）协议搭建分布式的目录系统。
- 秘钥备份及恢复：当用户证书生成时，秘钥被CA备份存储。当秘钥丢失需要恢复时，用户只需向CA提出申请，CA就会为用户自动进行秘钥恢复。
- 证书撤销处理：证书和秘钥都有一定的生成期限。当用户的私钥泄露或公司职员离职时，都需要撤销原CA证书。被撤销的CA证书进入证书库的黑名单，公众可查询核实。
- PKI应用接口是使用者和PKI交互的唯一途径，可以看成是PKI的客户端软件。使用者在其计算机上安装PKI的客户端软件。

PKI平台包括以上四个基本功能模块和一个应用接口模块。
### 4.3.2 基于X.509的PKI系统
基于X.509的PKI系统：
- X.509是国际电信联盟-电信（ITU-T）部分标准和国际标准化组织（ISO）的证书格式标准。
- X.509的主要作用是确定了公钥证书结构的基准。
    - 当前使用的版本是X.509 V3。
    - X.509 V3证书包括一组按预定义顺序排列的强制字段，还有可选扩展字段。即使在强制字段中，X.509证书也具有很大的灵活性，因为它为大多数字段提供了多种编码方案。

X.509的CA目录的层次结构：
- 基于X. 509的层次型认证机构分布：
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211207092017.png)
- 用户a的证书链可以使用下面的形式表达：
    - KRA《CAB》KRB《CAC》KRC《CAD》KRD《CAa》
    - KRX表示X的私钥签名，CAX表示X的证书。
- 如果某用户x希望验证用户a的证书。
    - 用户a的CA证书是认证机构D签发的，用户x只要得到认证机构D的公钥，就可以验证用户a的证书中D的签名，即完成对用户a的证书的认证，从而得到用户a的公钥。
    - 假如用户x不能确定D的公钥，就必须查看D的证书。
        - 由于D的证书是由认证机构C签发的，因此，用户x需要使用C的公钥验证D的证书并得到其公钥。
    - 以此类推，最坏的情况是用户x需要使用认证机构A的公钥，而A是此认证机构的根，A的证书也叫根证书，是使用其私钥自签名产生的。
        - 用户在使用CA证书之前必须先下载安装A的证书，同时系统会自动加载保存认证机构A的公钥。

一个典型的PKI模型：
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211207092249.png)

PKI系统功能：
- 接收验证用户数字证书的申请；
- 确定是否接受用户数字证书的申请；
- 向申请者颁发（或拒绝颁发）数字证书；
- 接收、处理用户的数字证书更新请求；
- 接收用户数字证书的查询、撤销；
- 产生和发布证书的有效期；
- 数字证书的归档；
- 密钥归档；
- 历史数据归档。
