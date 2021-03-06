Linux平台安全备忘录
================================================================================
这是一组Linux基金会自己系统管理员的推荐规范。所有Linux基金会的雇员都是远程工作，我们使用这套指导方针确保系统管理员的系统通过核心安全需求，降低我们平台成为攻击目标的风险。

即使你的系统管理员不用远程工作，很有可能的是，很多人的工作是在一个便携的笔记本上完成的，或者在业余时间或紧急时刻他们在工作平台中部署自己的家用系统。不论发生何种情况，你都能对应这个规范匹配到你的环境中。

这绝不是一个详细的“工作站加固”文档，可以说这是一个努力避免大多数明显安全错误导致太多不便的一组规范的底线。你可能阅读这个文档会认为它的方法太偏执，同时另一些人也许会认为这仅仅是一些肤浅的研究。安全就像在高速公路上开车 -- 任何比你开的慢的都是一个傻瓜，然而任何比你开的快的人都是疯子。这个指南仅仅是一些列核心安全规则，既不详细又不是替代经验，警惕，和常识。

每一节都分为两个部分：

- 核对适合你项目的需求
- 随意列出关心的项目，解释为什么这么决定

## 严重级别

在清单的每一个项目都包括严重级别，这些是我们希望能帮助指导你的决定：

- _(关键)_ 项目应该在考虑列表上被明确的重视。如果不采取措施，将会导致你的平台安全出现高风险。
- _(中等)_ 项目将改善你的安全形态，但不是很重要，尤其是如果他们太多的干涉你的工作流程。
- _(低等)_ 项目也许会改善整体安全性，但是在便利权衡下也许并不值得。
- _(可疑)_ 留作感觉会明显完善我们平台安全的项目，但是可能会需要大量的调整与操作系统交互的方式。

记住，这些只是参考。如果你觉得这些严重级别不能表达你的工程对安全承诺，正如你所见你应该调整他们为你合适的。

## 选择正确的硬件

我们禁止管理员使用一个特殊供应商或者一个特殊的型号，所以在选择工作系统时这部分是核心注意事项。

### 清单

- [ ] 系统支持安全启动 _(关键)_
- [ ] 系统没有火线，雷电或者扩展卡接口 _(中等)_
- [ ] 系统有TPM芯片 _(低)_

### 注意事项

#### 安全引导

尽管它是有争议的性质，安全引导提供了对抗很多针对平台的攻击（Rootkits, "Evil Maid,"等等），没有介绍太多额外的麻烦。它将不会停止真正专用的攻击者，加上有很大程度上,站点安全机构有办法应对它(可能通过设计),但是拥有安全引导总比什么都没有强。

作为选择，你也许部署了[Anti Evil Maid][1]提供更多健全的保护，对抗安全引导支持的攻击类型，但是它需要更多部署和维护的工作。

#### 系统没有火线，雷电或者扩展卡接口

火线是一个标准，故意的，允许任何连接设备完全直接内存访问你的系统（[查看维基百科][2]）。雷电接口和扩展卡同样有问题，虽然一些后来部署的雷电接口试图限制内存访问的范围。如果你没有这些系统端口，那是最好的，但是它并不严重，他们通常可以通过UEFI或内核本身禁用。

#### TPM芯片

可信平台模块（TPM）是主板上的一个与核心处理器单独分开的加密芯片，他可以用来增加平台的安全性（比如存储完整磁盘加密密钥），不过通常不用在日常平台操作。最多，这是个很好的存在，除非你有特殊需要使用TPM增加你平台安全性。

## 预引导环境

这是你开始安装系统前的一系列推荐规范。

### 清单

- [ ] 使用UEFI引导模式（不是传统BIOS）_(关键)_
- [ ] 进入UEFI配置需要使用密码 _(关键)_
- [ ] 使用安全引导 _(关键)_
- [ ] 启动系统需要UEFI级别密码 _(低)_

### 注意事项

#### UEFI和安全引导

UEFI尽管有缺点，还是提供很多传统BIOS没有的好功能，比如安全引导。大多数现代的系统都默认使用UEFI模式。

UEFI配置模式密码要确保密码强度。注意，很多厂商默默地限制了你使用密码长度，所以对比长口令你也许应该选择高熵短密码（更多地密码短语看下面）。

基于你选择的Linux分支，你也许会也许不会跳过额外的圈子，以导入你的发行版的安全引导键，才允许你启动发行版。很多分支已经与微软合作大多数厂商给他们已发布的内核签订密钥，这已经是大多数厂商公认的了，因此为了避免问题你必须处理密钥导入。

作为一个额外的措施，在允许某人得到引导分区然后尝试做一些不好的事之前，让他们输入密码。为了防止肩窥，这个密码应该跟你的UEFI管理密码不同。如果你关闭启动太多，你也许该选择别把心思费在这上面，当你已经进入LUKS密码，这将为您节省一些额外的按键。

## 发行版选择注意事项

很有可能你会坚持一个广泛使用的发行版如Fedora，Ubuntu，Arch，Debian，或他们的一个类似分支。无论如何，这是你选择使用发行版应该考虑的。

### 清单

- [ ] 拥有一个强健的MAC/RBAC系统（SELinux/AppArmor/Grsecurity） _(关键)_
- [ ] 公开的安全公告 _(关键)_
- [ ] 提供及时的安全补丁 _(关键)_
- [ ] 提供密码验证的包 _(关键)_
- [ ] 完全支持UEFI和安全引导 _(关键)_
- [ ] 拥有健壮的原生全磁盘加密支持 _(关键)_

### 注意事项

#### SELinux，AppArmor，和GrSecurity/PaX

强制访问控制（MAC）或者基于角色的访问控制（RBAC）是一个POSIX系统遗留的基于用户或组的安全机制延伸。这些天大多数发行版已经绑定MAC/RBAC系统（Fedora，Ubuntu），或通过提供一种机制一个可选的安装后的步骤来添加它（Gentoo，Arch，Debian）。很明显，强烈建议您选择一个预装MAC/RBAC系统的分支，但是如果你对一个分支情有独钟，没有默认启用它，装完系统后应计划配置安装它。

应该坚决避免使用不带任何MAC/RBAC机制的分支，像传统的POSIX基于用户和组的安全在当今时代应该算是考虑不足。如果你想建立一个MAC/RBAC工作站，通常会考虑AppArmor和PaX，他们比SELinux更容易学习。此外，在一个工作站上，有很少或者没有额外的监听用户运行的应用造成的最高风险，GrSecurity/PaX_可能_会比SELinux提供更多的安全效益。

#### 发行版安全公告

大多数广泛使用的分支都有一个机制发送安全公告到他们的用户，但是如果你对一些机密感兴趣，查看开发人员是否有记录机制提醒用户安全漏洞和补丁。缺乏这样的机制是一个重要的警告信号，这个分支不够成熟,不能被视为主要管理工作站。

#### 及时和可靠的安全更新

多数常用的发行版提供的定期安全更新，但为确保关键包更新及时提供是值得检查的。避免使用分支和"社区重建"的原因是，由于不得不等待上游分支先发布它，他们经常延迟安全更新。

你如果找到一个在安装包，更新元数据，或两者上不使用加密签名的发行版，将会处于困境。这么说，常用的发行版多年前就已经知道这个基本安全的意义（Arch，我正在看你），所以这也是值得检查的。

#### 发行版支持UEFI和安全引导

检查发行版支持UEFI和安全引导。查明它是否需要导入额外的密钥或是否要求启动内核有一个已经被系统厂商信任的密钥签名（例如跟微软达成合作）。一些发行版不支持UEFI或安全启动，但是提供了替代品来确保防篡改或防破坏引导环境（[Qubes-OS][3]使用Anti Evil Maid，前面提到的）。如果一个发行版不支持安全引导和没有机制防止引导级别攻击，还是看看别的吧。

#### 全磁盘加密

全磁盘加密是保护静止数据要求，大多数发行版都支持。作为一个选择方案，系统自加密硬件驱动也许用来（通常通过主板TPM芯片实现）和提供类似安全级别加更快的选项，但是花费也更高。

## 发行版安装指南

所有发行版都是不同的，但是也有一些一般原则：

### 清单

- [ ] 使用健壮的密码全磁盘加密（LUKS） _(关键)_
- [ ] 确保交换分区也加密了 _(关键)_
- [ ] 确保引导程序设置了密码（可以和LUKS一样） _(关键)_
- [ ] 设置健壮的root密码（可以和LUKS一样） _(关键)_
- [ ] 使用无特权账户登录，管理员组的一部分 _(关键)_
- [ ] 设置强壮的用户登录密码，不同于root密码 _(关键)_

### 注意事项

#### 全磁盘加密

除非你正在使用自加密硬件设备，配置你的安装程序给磁盘完整加密用来存储你的数据与你的系统文件很重要。通过自动安装的cryptfs循环文件加密用户目录还不够简单（我正在看你，老版Ubuntu），这并没有给系统二进制文件或交换分区提供保护，它可能包含大量的敏感数据。推荐的加密策略是加密LVM设备，所以在启动过程中只需要一个密码。

`/boot`分区将一直保持非加密，当引导程序需要引导内核前，调用LUKS/dm-crypt。内核映像本身应该用安全引导加密签名检查防止被篡改。

换句话说，`/boot`应该是你系统上唯一没有加密的分区。

#### 选择好密码

现代的Linux系统没有限制密码口令长度，所以唯一的限制是你的偏执和倔强。如果你要启动你的系统，你将大概至少要输入两个不同的密码：一个解锁LUKS，另一个登陆，所以长密码将会使你老的很快。最好从丰富或混合的词汇中选择2-3个单词长度，容易输入的密码。

优秀密码例子（是的，你可以使用空格）：
- nature abhors roombas
- 12 in-flight Jebediahs
- perdon, tengo flatulence

如果你更喜欢输入口令句，你也可以坚持使用无词汇密码但最少要10-12个字符长度。

除非你有人身安全的担忧，写下你的密码，并保存在一个远离你办公桌的安全的地方才合适。

#### Root，用户密码和管理组

我们建议，你的root密码和你的LUKS加密使用同样的密码（除非你共享你的笔记本给可信的人，他应该能解锁设备，但是不应该能成为root用户）。如果你是笔记本电脑的唯一用户,那么你的root密码与你的LUKS密码不同是没有意义的安全优势。通常，你可以使用同样的密码在你的UEFI管理，磁盘加密，和root登陆 -- 知道这些任意一个都会让攻击者完全控制您的系统，在单用户工作站上使这些密码不同，没有任何安全益处。

你应该有一个不同的，但同样强健的常规用户帐户密码用来每天工作。这个用户应该是管理组用户（例如`wheel`或者类似，根据分支），允许你执行`sudo`来提升权限。

换句话说，如果在你的工作站只有你一个用户，你应该有两个独特的，强健的，同样的强壮的密码需要记住：

**管理级别**，用在以下区域：

- UEFI管理
- 引导程序（GRUB）
- 磁盘加密（LUKS）
- 工作站管理（root用户）

**User-level**, used for the following:
**用户级别**，用在以下：

- 用户登陆和sudo
- 密码管理器的主密码

很明显,如果有一个令人信服的理由他们所有可以不同。

## 安装后的加强

安装后的安全性加强在很大程度上取决于你选择的分支，所以在一个通用的文档中提供详细说明是徒劳的，例如这一个。然而,这里有一些你应该采取的步骤:

### 清单

- [ ] 在全体范围内禁用火线和雷电模块 _(关键)_
- [ ] 检查你的防火墙,确保过滤所有传入端口 _(关键)_
- [ ] 确保root邮件转发到一个你可以查看到的账户 _(关键)_
- [ ] 检查以确保sshd服务默认情况下是禁用的 _(中等)_
- [ ] 建立一个系统自动更新任务,或更新提醒 _(中等)_
- [ ] 配置屏幕保护程序在一段时间的不活动后自动锁定 _(中等)_
- [ ] 建立日志监控 _(中等)_
- [ ] 安装使用rkhunter _(低等)_
- [ ] 安装一个入侵检测系统 _(偏执)_

### 注意事项

#### 黑名单模块

将火线和雷电模块列入黑名单，增加一行到`/etc/modprobe.d/blacklist-dma.conf`文件：

    blacklist firewire-core
    blacklist thunderbolt

重启后的模块将被列入黑名单。这样做是无害的，即使你没有这些端口(但也不做任何事)。

#### Root邮件

默认的root邮件只是存储在系统基本上没人读过。确保你设置了你的`/etc/aliases`来转发root邮件到你确实能读取的邮箱，否则你也许错过了重要的系统通知和报告：

    # Person who should get root's mail
    root:          bob@example.com

编辑后这些后运行`newaliases`，然后测试它确保已投递，像一些邮件供应商将拒绝从没有或者不可达的域名的邮件。如果是这个原因，你需要配置邮件转发直到确实可用。

#### 防火墙，sshd，和监听进程

默认的防火墙设置将取决于您的发行版，但是大多数都允许`sshd`端口连入。除非你有一个令人信服的合理理由允许连入ssh，你应该过滤出来,禁用sshd守护进程。

    systemctl disable sshd.service
    systemctl stop sshd.service

如果你需要使用它，你也可以临时启动它。

通常，你的系统不应该有任何侦听端口除了响应ping。这将有助于你对抗网络级别的零日漏洞利用。

#### 自动更新或通知

建议打开自动更新，除非你有一个非常好的理由不这么做，如担心自动更新将使您的系统无法使用（这是发生在过去,所以这种恐惧并非杞人忧天）。至少，你应该启用自动通知可用的更新。大多数发行版已经有这个服务自动运行，所以你不需要做任何事。查阅你的发行版文档查看更多。

你应该尽快应用所有明显的勘误，即使这些不是特别贴上“安全更新”或有关联的CVE代码。所有错误都潜在的安全漏洞和新的错误，比起坚持旧的，已知的错误，未知错误通常是更安全的策略。

#### 监控日志

你应该对你的系统上发生了什么很感兴趣。出于这个原因，你应该安装`logwatch`然后配置它每夜发送在你的系统上发生的任何事情的活动报告。这不会预防一个专业的攻击者，但是一个好安全网功能。

注意,许多systemd发行版将不再自动安装一个“logwatch”需要的syslog服务（由于systemd依靠自己的分类），所以你需要安装和启用“rsyslog”来确保使用logwatch之前你的/var/log不是空。

#### Rkhunter和IDS

安装`rkhunter`和一个入侵检测系统（IDS）像`aide`或者`tripwire`将不会有用，除非你确实理解他们如何工作采取必要的步骤来设置正确（例如，保证数据库在额外的媒介，从可信的环境运行检测，记住执行系统更新和配置更改后要刷新数据库散列，等等）。如果你不愿在你的工作站执行这些步骤调整你如何工作，这些工具将带来麻烦没有任何实在的安全益处。

我们强烈建议你安装`rkhunter`并每晚运行它。它相当易于学习和使用，虽然它不会阻止一个复杂的攻击者，它也能帮助你捕获你自己的错误。

## 个人工作站备份

工作站备份往往被忽视，或无计划的做，常常是不安全的方式。

### 清单

- [ ] 设置加密备份工作站到外部存储 _(关键)_
- [ ] 使用零认知云备份的备份工具 _(中等)_

### 注意事项

#### 全加密备份存到外部存储

把全部备份放到一个移动磁盘中比较方便，不用担心带宽和流速（在这个时代，大多数供应商仍然提供显著的不对称的上传/下载速度）。不用说，这个移动硬盘本身需要加密（又一次，通过LIKS），或者你应该使用一个备份工具建立加密备份，例如`duplicity`或者它的GUI版本，`deja-dup`。我建议使用后者并使用随机生成的密码，保存到你的密码管理器中。如果你带上笔记本去旅行，把这个磁盘留在家，以防你的笔记本丢失或被窃时可以找回备份。

除了你的家目录外，你还应该备份`/etc`目录和处于鉴定目的的`/var/log`目录。

首先是，避免拷贝你的家目录到任何非加密存储上，甚至是快速的在两个系统上移动文件，一旦完成你肯定会忘了清除它，暴露个人隐私或者安全信息到监听者手中 -- 尤其是把这个存储跟你的笔记本防盗同一个包里。

#### 零认知站外备份选择性

站外备份也是相当重要的，是否可以做到要么需要你的老板提供空间，要么找一家云服务商。你可以建一个单独的duplicity/deja-dup配置，只包括重要的文件，以免传输大量你不想备份的数据（网络缓存，音乐，下载等等）。

作为选择，你可以使用零认知备份工具，例如[SpiderOak][5]，它提供一个卓越的Linux GUI工具还有实用的特性，例如在多个系统或平台间同步内容。

## 最佳实践

下面是我们认为你应该采用的最佳实践列表。它当然不是非常详细的，而是试图提供实用的建议,一个可行的整体安全性和可用性之间的平衡

### 浏览

毫无疑问，在你的系统上web浏览器将是最大、最容易暴露的攻击层面的软件。它是专门下载和执行不可信，恶意代码的一个工具。它试图采用沙箱和代码卫生处理等多种机制保护你免受这种危险，但是在之前多个场合他们都被击败了。你应该学到浏览网站是最不安全的活动在你参与的任何一天。

有几种方法可以减少浏览器的影响，但真正有效的方法需要你操作您的工作站将发生显著的变化。

#### 1: 实用两个不同的浏览器

这很容易做到，但是只有很少的安全效益。并不是所有浏览器都妥协给攻击者完全自由访问您的系统 -- 有时他们只能允许一个读取本地浏览器存储，窃取其他标签的活动会话，捕获输入浏览器，例如，实用两个不同的浏览器，一个用在工作/高安全站点，另一个用在其他，有助于防止攻击者请求整个饼干罐的小妥协。主要的不便是两个不同的浏览器消耗内存大量。

我们建议：

##### 火狐用来工作和高安全站点

使用火狐登陆工作有关的站点，应该额外关心的是确保数据如cookies，会话，登陆信息，打键次数等等，明显不应该落入攻击者手中。除了少数的几个网站，你不应该用这个浏览器访问其他网站。

你应该安装下面的火狐扩展：

- [ ] NoScript _(关键)_
  - NoScript阻止活动内容加载，除非在用户白名单里的域名。跟你默认浏览器比它使用起来很麻烦（可是提供了真正好的安全效益），所以我们建议只在开启了它的浏览器上访问与工作相关的网站。

- [ ] Privacy Badger _(关键)_
  - EFF的Privacy Badger将在加载时预防大多数外部追踪器和广告平台，在这些追踪站点影响你的浏览器时将有助于避免妥协（追踪着和广告站点通常会成为攻击者的目标，因为他们会迅速影响世界各地成千上万的系统）。

- [ ] HTTPS Everywhere _(关键)_
  - 这个EFF开发的扩展将确保你访问的大多数站点都在安全连接上，甚至你点击的连接使用的是http://（有效的避免大多数的攻击，例如[SSL-strip][7]）。

- [ ] Certificate Patrol _(中等)_
  - 如果你正在访问的站点最近改变了他们的TLS证书 -- 特别是如果不是接近失效期或者现在使用不同的证书颁发机构，这个工具将会警告你。它有助于警告你是否有人正尝试中间人攻击你的连接，但是产生很多无害的假的类似情况。

你应该让火狐成为你的默认打开连接的浏览器，因为NoScript将在加载或者执行时阻止大多数活动内容。

##### 其他一切都用Chrome/Chromium

Chromium开发者在增加很多很好的安全特性方面比火狐强（至少[在Linux上][6])），例如seccomp沙箱，内核用户名空间等等，这担当一个你访问网站和你其他系统间额外的隔离层。Chromium是流开源项目，Chrome是Google所有的基于它构建的包（使用它输入时要非常谨慎任，何你不想让谷歌知道的事情都不要使用它）。

有人推荐你在Chrome上也安装**Privacy Badger**和**HTTPS Everywhere**扩展，然后给他一个不同的主题，从火狐指出这是你浏览器“不信任的站点”。

#### 2: 使用两个不同浏览器，一个在专用的虚拟机里

这有点像上面建议的做法，除了您将添加一个额外的步骤，通过快速访问协议运行专用虚拟机内部Chrome，允许你共享剪贴板和转发声音事件（如，Spice或RDP）。这将在不可信的浏览器和你其他的工作环境之间添加一个优秀的隔离层，确保攻击者完全危害你的浏览器将不得不另外打破VM隔离层，以达到系统的其余部分。

这是一个出奇可行的结构,但是需要大量的RAM和高速处理器可以处理增加的负载。这还需要一个重要的奉献的管理员需要相应地调整自己的工作实践。

#### 3: 通过虚拟化完全隔离你的工作和娱乐环境

看[Qubes-OS项目][3]，它致力于通过划分你的应用到完全独立分开的VM中，提供高安全工作环境。

### 密码管理器

#### 清单

- [ ] 使用密码管理器 _(关键)_
- [ ] 不相关的站点使用不同的密码 _(关键)_
- [ ] 使用支持团队共享的密码管理器 _(中等)_
- [ ] 给非网站用户使用一个单独的密码管理器 _(偏执)_

#### 注意事项

使用好的，唯一的密码对你的团队成员来说应该是非常关键的需求。证书盗取一直在发生 — 要么通过中间计算机，盗取数据库备份，远程站点利用，要么任何其他的打算。证书从不应该通过站点被重用，尤其是关键的应用。


##### 浏览器中的密码管理器

每个浏览器有一个比较安全的保存密码机制，通过供应商的机制可以同步到云存储同事用户提供密码保证数据加密。无论如何，这个机制有严重的劣势：


1. 不能跨浏览器工作
2. 不提供任何与团队成员共享凭证的方法

也有一些良好的支持，免费或便宜的密码管理器，很好的融合到多个浏览器，跨平台工作，提供小组共享（通常是支付服务）。可以很容易地通过搜索引擎找到解决方案。

##### 独立的密码管理器

任何密码管理器都有一个主要的缺点，与浏览器结合，事实上是应用的一部分，这样最有可能被入侵者攻击。如果这让你不舒服（应该这样），你应该选择两个不同的密码管理器 -- 一个集成在浏览器中用来保存网站密码，一个作为独立运行的应用。后者可用于存储高风险凭证如root密码，数据库密码，其他shell账户凭证等。

有这样的工具可以特别有效的在团腿成员间共享超级用户的凭据（服务器根密码，ILO密码，数据库管理密码，引导装载程序密码等等）。

这几个工具可以帮助你：

- [KeePassX][8]，2版中改善了团队共享
- [Pass][9]，它使用了文本文件和PGP并与git结合
- [Django-Pstore][10]，他是用GPG在管理员之间共享凭据
- [Hiera-Eyaml][11]，如果你已经在你的平台中使用了Puppet，可以便捷的追踪你的服务器/服务凭证，像你的Hiera加密数据的一部分。

### 加固SSH和PGP私钥

个人加密密钥，包括SSH和PGP私钥，都是你工作站中最重要的物品 -- 攻击将在获取到感兴趣的东西，这将允许他们进一步攻击你的平台或冒充你为其他管理员。你应该采取额外的步骤，确保你的私钥免遭盗窃。

#### 清单

- [ ] 强壮的密码用来保护私钥 _(关键)_
- [ ] PGP的主密码保存在移动存储中 _(中等)_
- [ ] 身份验证、签名和加密注册表子项存储在智能卡设备 _(中等)_
- [ ] SSH配置为使用PGP认证密钥作为ssh私钥 _(中等)_

#### 注意事项

防止私钥被偷的最好方式是使用一个智能卡存储你的加密私钥，不要拷贝到工作平台上。有几个厂商提供支持OpenPGP的设备：

- [Kernel Concepts][12]，在这里可以采购支持OpenPGP的智能卡和USB读取器，你应该需要一个。
- [Yubikey NEO][13]，这里提供OpenPGP功能的智能卡还提供很多很酷的特性（U2F, PIV, HOTP等等）。 

确保PGP主密码没有存储在工作平台也很重要，只有子密码在使用。主密钥只有在登陆其他的密钥和创建子密钥时使用 — 不经常发生这种操作。你可以照着[Debian的子密钥][14]向导来学习如何移动你的主密钥到移动存储和创建子密钥。

你应该配置你的gnupg代理作为ssh代理然后使用基于智能卡PGP认证密钥作为你的ssh私钥。我们公布了一个细节向导如何使用智能卡读取器或Yubikey NEO。

如果你不想那么麻烦，最少要确保你的PGP私钥和你的SSH私钥有个强健的密码，这将让攻击者很难盗取使用它们。

### 工作站上的SELinux

如果你使用的发行版绑定了SELinux（如Fedora），这有些如何使用它的建议，让你的工作站达到最大限度的安全。

#### 清单

- [ ] 确保你的工作站强制使用SELinux _(关键)_
- [ ] 不要盲目的执行`audit2allow -M`，经常检查  _(关键)_
- [ ] 从不 `setenforce 0` _(中等)_
- [ ] 切换你的用户到SELinux用户`staff_u` _(中等)_

#### 注意事项

SELinux是一个强制访问控制（MAC）为POSIX许可核心功能扩展。它是成熟，强健，自从它推出以来已经有很长的路了。不管怎样，许多系统管理员现在重复过时的口头禅“关掉它就行。”

话虽如此，在工作站上SELinux还是限制了安全效益，像很多应用都要作为一个用户自由的运行。开启它有益于给网络提供足够的保护，有可能有助于防止攻击者通过脆弱的后台服务提升到root级别的权限用户。

我们的建议是开启它并强制使用。

##### 从不`setenforce 0`

使用`setenforce 0`短时间内把SELinux设置为许可模式，但是你应该避免这样做。其实你是想查找一个特定应用或者程序的问题，实际上这样是把全部系统的SELinux关闭了。

你应该使用`semanage permissive -a [somedomain_t]`替换`setenforce 0`，只把这个程序放入许可模式。首先运行`ausearch`查看那个程序发生问题：

    ausearch -ts recent -m avc

然后看下`scontext=`（SELinux的上下文）行，像这样：

    scontext=staff_u:staff_r:gpg_pinentry_t:s0-s0:c0.c1023
                             ^^^^^^^^^^^^^^

这告诉你程序`gpg_pinentry_t`被拒绝了，所以你想查看应用的故障，应该增加它到许可模式：

    semange permissive -a gpg_pinentry_t

这将允许你使用应用然后收集AVC的其他部分，你可以连同`audit2allow`写一个本地策略。一旦完成你就不会看到新的AVC的拒绝，你可以从许可中删除程序，运行：

    semanage permissive -d gpg_pinentry_t

##### 用SELinux的用户staff_r，使用你的工作站

SELinux附带的本地角色实现基于角色的用户帐户禁止或授予某些特权。作为一个管理员，你应该使用`staff_r`角色，这可以限制访问很多配置和其他安全敏感文件，除非你先执行`sudo`。

默认，用户作为`unconfined_r`被创建，你可以运行大多数应用，没有任何（或只有一点）SELinux约束。转换你的用户到`staff_r`角色，运行下面的命令：

    usermod -Z staff_u [username]

你应该退出然后登陆激活新角色，届时如果你运行`id -Z`，你将会看到：

    staff_u:staff_r:staff_t:s0-s0:c0.c1023

在执行`sudo`时，你应该记住增加一个额外的标准告诉SELinux转换到"sysadmin"角色。你想要的命令是：

    sudo -i -r sysadm_r

届时`id -Z`将会显示：

    staff_u:sysadm_r:sysadm_t:s0-s0:c0.c1023

**警告**：在进行这个切换前你应该舒服的使用`ausearch`和`audit2allow`，当你作为`staff_r`角色运行时你的应用有可能不再工作了。写到这里时，以下流行的应用已知在`staff_r`下没有做策略调整就不会工作：

- Chrome/Chromium
- Skype
- VirtualBox

切换回`unconfined_r`，运行下面的命令：

    usermod -Z unconfined_u [username]

然后注销再重新回到舒服的区域。

## 延伸阅读

IT安全的世界是一个没有底的兔子洞。如果你想深入，或者找到你的具体发行版更多的安全特性，请查看下面这些链接：

- [Fedora Security Guide](https://docs.fedoraproject.org/en-US/Fedora/19/html/Security_Guide/index.html)
- [CESG Ubuntu Security Guide](https://www.gov.uk/government/publications/end-user-devices-security-guidance-ubuntu-1404-lts)
- [Debian Security Manual](https://www.debian.org/doc/manuals/securing-debian-howto/index.en.html)
- [Arch Linux Security Wiki](https://wiki.archlinux.org/index.php/Security)
- [Mac OSX Security](https://www.apple.com/support/security/guides/)

## 许可

这项工作在[创作共用授权4.0国际许可证][0]许可下。

--------------------------------------------------------------------------------

via: https://github.com/lfit/itpol/blob/master/linux-workstation-security.md#linux-workstation-security-list

作者：[mricon][a]
译者：[wyangsun](https://github.com/wyangsun)
校对：[校对者ID](https://github.com/校对者ID)

本文由 [LCTT](https://github.com/LCTT/TranslateProject) 原创编译，[Linux中国](https://linux.cn/) 荣誉推出

[a]:https://github.com/mricon
[0]: http://creativecommons.org/licenses/by-sa/4.0/
[1]: https://github.com/QubesOS/qubes-antievilmaid
[2]: https://en.wikipedia.org/wiki/IEEE_1394#Security_issues
[3]: https://qubes-os.org/
[4]: https://xkcd.com/936/
[5]: https://spideroak.com/
[6]: https://code.google.com/p/chromium/wiki/LinuxSandboxing
[7]: http://www.thoughtcrime.org/software/sslstrip/
[8]: https://keepassx.org/
[9]: http://www.passwordstore.org/
[10]: https://pypi.python.org/pypi/django-pstore
[11]: https://github.com/TomPoulton/hiera-eyaml
[12]: http://shop.kernelconcepts.de/
[13]: https://www.yubico.com/products/yubikey-hardware/yubikey-neo/
[14]: https://wiki.debian.org/Subkeys
[15]: https://github.com/lfit/ssh-gpg-smartcard-config
