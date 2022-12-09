# 中文 man 手册页计划

"manpages-zh"

中文 man 手册页 | Chinese Manual Pages

## 简介

本项目对历史上存留下来的中文手册页进行整理、维护与翻新，
并从 2016 年末开始作为某些 Linux 发行版中`manpages-zh`软件包的上游。

### 收录准则

本项目欢迎各类贡献，尤其欢迎各种软件最新版本手册页的中文翻译。

由于需要收录于 Linux 发行版中，
故历史存留的过时 BSD 手册页将从新版本中移除，敬请谅解。
您仍然可以在发布历史中找到过往的手册页。

### 授权

本项目延续历史上的授权，使用 GFDL-1.2+ 发布，且为了符合
DFSG（DFSG-Compatible）而不包含不可变章节。个别手册页受限于
上游源文件可能以其它许可证发布（如 GPL）。

```
Permission is granted to copy, distribute and/or modify this document
under the terms of the GNU Free Documentation License, Version 1.2
or any later version published by the Free Software Foundation;
with no Invariant Sections, no Front-Cover Texts, and no Back-Cover
Texts.

However, some translated manual pages maybe released under different
licenses since they are derivative works of original (English) manual
pages, especially GPL-licensed documents. Please refer to the header
information in each file for its exact license.
```

向本项目提交补丁则代表您同意按照上述许可证发布您的修改与贡献。

请查看 COPYING 文件了解具体条款。`COPYRIGHT`
文件以机器可读的方式记录了文件的版权信息。

#### 历史文档的签名问题

所有 CMPP 文档均在文末附有签名。如果后续修订仅在原有文档基础上改进，
则签名内容须保留。若从头开始翻译（例如使用 `po4a`），
则新翻译文档无需保留原签名。

## 安装与使用

### 构建

构建依赖包括：

* cmake 3.12+
* python3
* opencc 1.x

### “编译”安装（CMake）

```bash
mkdir build && cd build/
cmake ..
make
make install # 需要超级用户权限
```

可用的 CMake 参数：

* `ENABLE_APPEND_COLOPHON`: 在手册页末尾添加翻译人员信息
* `ENABLE_ZHCN`: 生成并安装简体中文手册页
* `ENABLE_ZHTW`: 生成并安装繁体中文手册页

### 从 Linux 发行版安装

Debian / Ubuntu:
```bash
sudo apt update
sudo apt install manpages-zh
```

Arch Linux:
```bash
pacman -Syu
pacman -S man-pages-zh_cn man-pages-zh_tw
```

Red Hat / CentOS:
```bash
yum update
yum install man-pages-zh-CN
```

Fedora:
```bash
dnf update
dnf install man-pages-zh-CN
```

**注意**：RHEL/CentOS 6/7 及 Fedora 24 以前的软件源中提供的版本于原 i18n-zh 项目时期发布，本项目不提供正式支持。红帽系发行版未提供繁体/正体中文版本。

安装后，使用语言对应的 locale 运行 man 查阅手册页时会优先查阅翻译版本。

### 使用

对大多数现代 Linux 发行版用户来说，安装后，正常使用 `man` 命令即可。在您的系统设置为中文环境时，
如果有对应的中文手册页，则该手册页将显示中文版本。如系统并非中文环境，请临时调整您的环境变量以使用中文内容。

对苹果系统用户来说，本项目并未在其平台上进行测试，您可能需要手动进行某些调整。
据用户反馈，macos 上默认无法正常显示中文字符，需要进行调整。请参考 [issue #3](https://github.com/man-pages-zh/manpages-zh/issues/3) 了解某些用户的测试情况。


为了确保您的阅览体验最佳，请保证您系统上的 groff 为最新版本（1.22.4）。
旧版 groff 可能对中文没有良好支持。

您还可以在线阅读手册页内容：[点我前往](https://manpages.debian.org/unstable/manpages-zh/index.html)（在线渲染由 Debian 提供）

## 贡献项目

我们十分欢迎您的参与，为该项目出一份力。有以下两种方式可以参与到项目开发中：

### 提交已翻译手册页

如果您对某个手册页做了翻译或翻译的改进，您可以提交一份完整的手册页以便收录。
为保证您的手册页起到最大作用，请确认其内容可以使用 GFDL-1.2+ 兼容的授权协议，
并在向本项目提交前先与上游联系以确认能否被上游收录。

### 参与翻译

本项目翻新的翻译工作在 `translation` 文件夹（子模块）下，使用标准 gettext PO
文档形式进行翻译。欢迎参与翻译工作，请访问[对应仓库](https://github.com/man-pages-zh/man-pages-translation)
以了解详情。

无论您以哪种方式贡献，请在提交的同时将您的名字加入贡献者列表（`AUTHORS`文件）中。

* * *

## 项目历史

### CMPP 时期

项目最早发起时被称为“中国linux论坛中文手册页翻译计划”，主页为
cmpp.linuxforum.net（已失效）。CMPP项目发布 `manpages-zh` 项目
至 1.5 版本。

下面是 manpages-zh 1.5 版本的说明。其内容早已过时，仅供参考：

```
这是来自CMPP <http://cmpp.linuxforum.net> 的中文手册页。请到
http://sf.linuxforum.net/projects/cmpp 来查看项目的近况，取得最新的文件
下载；到 http://www.linuxforum.net 的讨论版上来参与工作。

在安装之前，运行 locale 命令来查看自己的语言环境是什么，主要有影响的是
LC_ALL，LC_CTYPE 和 LANG 变量的值。如果语言环境是 zh_CN.UTF-8，可以运行
make u8 && make install-u8；如果语言环境是 GB2312，GBK 或者 GB18030，可
以运行 make gb && make install-gb；或者，可以下载对应的rpm 包来安装，同
样简单。对于 zh_CN.UTF-8，应当安装 man-pages-zh_CN-1.x，对于 GB2312，GBK
，GB18030，应当安装 man-pages-zh_CN-gb-1.x。请参阅讨论版上关于不同发行版
的系统与其默认中文编码的说明。

需要说明的是，同时安装所有 rpm 也是没有问题的，不会对系统造成什么影响，
因为它们只是简单的文本文件。在安装 man-pages-zh_CN-gb-1.x 之后，应当注销
退出，在重新登录之后才能继续使用手册页系统；而在安装适于 UTF-8 语言环境
的 man-pages-zh_CN-1.x 之后，不必注销就可以用了。使用方法也有不同，对于
GB* 语言环境，应当使用 cman 命令来查看，例如 ``cman ls''；对于 UTF-8 语
言环境的系统，例如 Fedora Core 2，只要使用 man 命令就可以了。

所有已翻译文档的版权属于其翻译者，或由翻译者指定。文档所有者没有另外说明
的话，此软件包中的所有文档可以在遵循GNU FDL 的情况下重新发布。其他的文件
如果没有另外的说明，则版权归于FSF，遵循GNU GPL 条款发布。建议翻译者放弃
版权。

如有任何问题，请用电子邮件和本计划的协调人
	xuming<xuming@users.sourceforge.net>联系

源代码目录结构的说明如下：
debian：存放制作debian打包需要的所有内容
DOCS：	除了README 和README.GB, COPYING 之外的所有文档。
	其中自动生成的00TRANSLATED 只有英文版本，其他文档都提供UTF-8 和GB 两种版本。
raw:	原始的man文档，为便于校对而设立。要注意更新
src：	存放着生成安装包需要的原始的man文档以及为GB包准备的cman配置文件。
	从cman 的cvs中获得文档之后，应当将其转换为UTF-8 格式man文档，然
	后再复制到本目录的相应位置。转换需要的程序是iconv 和dos2unix，
	html格式也应转换为man格式再进入本目录。
utils：	存放着各种有用的脚本。
UTF-8, GB 目录中的内容是在执行make 时自动生成的。
另外根目录还有Makefile 以及man-pages-zh_CN.spec 用来控制打包。

参与翻译的成员请注意DOCS 目录提供的词汇表，src/man9 提供的中文翻译的两个
例子，还有以下内容：
1) 译者请将自己的信息放在署名中 (默认情况下译者是维护人)。如果译者不想管
   理自己的“产品”，也可以在署名中加以说明;
2) man手册中文版的署名的形式为 (一定要保留原英文作者名字，版权等信息，只
   要将下面一段复制粘贴到文档最后即可，注意空格):
.SH "[中文版维护人]"
.B 姓名 <email>
.SH "[中文版最新更新]"
.BR yyyy.mm.dd
.SH "[中国linux论坛中文手册页翻译计划]"
.BI http://cmpp.linuxforum.net
```

### LCTT 社区的工作

LCTT 手册页翻译项目主页：https://github.com/LCTT/man-pages

LCTT 的工作引入了 po4a，使得翻译工作能够转由 gettext 标准的 PO 文件进行，
大大降低了工作门槛。LCTT 下的手册页项目2014年以后不活跃。

LCTT 项目与本项目无直接关系，但对本项目的发展有借鉴意义。

### 作为 i18n-zh 子项目

在 CMPP 计划停滞后，i18n-zh 项目对其进行分支并继续维护数年直至陷入停滞状态。

以下是项目 README 相关原文。内容已过时，仅供参考：

```
** 本项目需要有人接手，想接手的人请提交 issue, 多谢 **

本项目的主页为: http://code.google.com/p/manpages-zh/

本项目(manpages-zh)为 i18n-zh 项目[1]的子项，从 CMPP (中文 Man Pages 计
划) 分支而来。

[1] http://code.google.com/p/i18n-zh

CMPP 项目现在可能已经死亡，原主页(cmpp.linuxforum.net)已不能访问。

本项目的目的是维护 CMPP 遗留下的成果，并对其错误/漏洞进行修改。

本项目使用 git 进行管理, git 主页位于
http://github.com/lidaobing/manpages-zh/tree/master

本项目将继续延用 CMPP 的 GFDL 1.2 的授权方式。为与 DFSG 兼容，将不包含不
可变章节(Invariant Sections)。

本项目欢迎大家提交补丁。
```

### “基于列数的manpages手册”

2015年有一个接手开发的尝试，项目主页[位于GitHub上](https://github.com/manpages-zh/manpages_zh)。

项目探索了多种绕过 roff 源码生成手册页的工作方式，包括 markdown / docbook / po4a 等。
项目于当年陷入停滞。

* * *

## 参考站点

* https://manned.org/
* http://manpages.ubuntu.com/
* https://manpages.debian.org/
* https://manpages-l10n-team.pages.debian.net/manpages-l10n/
