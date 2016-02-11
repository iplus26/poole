---
layout: post
title: 软件配置管理工具的简介与对比
description: "版本控制系统 (version control system)，是软件配置管理 SCM 的重要组成部分。在本文中，我对几种常用的软件配置管理工具的安装调试和功能特性进行对比。"
tags: [SCM, 软件配置管理, Git, SVN, CVS]
bannerColor: "gray"
bannerContent: "git-logo"
---

软件配置管理工具可以分成 3 个级别：

1. 版本控制工具，是入门级的工具，例如：CVS, VSS. 
2. 项目级配置管理工具，适合管理中小型的项目，在版本管理的基础上增加变更控制、状态统计的功能，例如：ClearCase、PVCS.
3. 企业级配置管理工具，在传统意义的配置管理的基础上又具有比较强的过程管理软件。

*以上一节摘自 MBA 智库百科。*

上述提到的 [CVS][1], 即 Concurrent Versions System, 是一个版本控制系统(version control system), 是软件配置管理 SCM 的重要组成部分。CVS 是一个开源软件，可以在 [GNU 网站][2]上免费下载安装。

我下载了安装程序之后没能在 Windows 10 上运行成功，加之如今大多数企业都已经使用 SVN 替代了 CVS 进行代码的版本控制，我将在下文主要介绍 SVN 而不是 CVS; Git 由于其分布式的特性，相比于 SVN 又拥有其独特的优势；VSS 和 ClearCase 分别是微软公司和 IBM 公司推出的软件配置管理工具。

## CVS

CVS，即 Concurrent Versions System, 是一个开源的版本控制系统，是软件配置管理 (SCM, Source Configuration Management) 的重要组成部分。

### 功能特点

* CVS 允许开发者进行多人协作，版本历史是存储在一个远程的中心服务器上，每个开发者的机器上有所有文件的副本。这样，能够允许开发者在全世界各地进行协同开发，他们甚至不需要足以在远程服务器上修改文件的网络，而仅仅需要能够和远程服务器同步，在本地客户端修改。
* Unreserved checkouts. 允许多个开发者同时在相同的文件上进行修改。
* CVS 提供了一个模块化的数据库，作为一个很大的软件的一部分。
* CVS 能够在 Unix, Windows, Linux 等多个平台上运行。

### 安装配置

CVS 作为一个开源软件，能够在 GUN 软件官网进行下载安装，找到对应操作系统版本的 CVS 版本，直接进行安装即可使用。

*链接: http://ftp.gnu.org/non-gnu/cvs*

## SVN
SVN 是 Subversion 的简称，是一个开源的版本控制系统，它的特点是采用了分支管理系统。如今多数企业都采用 SVN 进行代码的版本控制，比如我今年暑假实习的公司就是。

### 功能特点

#### 版本控制

对于每一次文件版本的提交，SVN 都记录在案，包括文件变更的位置、变更的原因等等。因此，我们可以很方便地查看每一次更新历史（以 commit 为准），抑或是在遇到重大错误的时候，能够回滚到之前的版本。

#### 统一的版本号

在 SVN 下，任何一次代码的提交都会将所有文件增加到一个统一的、新的版本号，即便是 commit 中没有涉及到的文件的版本号也会被更新。如此，同一个版本的代码和文件的版本都是一致的。

#### 原子提交

每一次提交，都是全部文件作为一个整体提交的，这样免去了数据库损坏的风险。

#### 团队支持

SVN 允许多人协作，并配备有用户组和用户权限的管理。通过设置不同用户组别的权限差异，能够实现团队内多人协作，并且不出现因为权限问题而导致的错误。

### 安装配置方法

#### 在 Mac 上安装 SVN

我用的是 Mac OS, 首先来介绍一下如何在 Mac 上安装 SVN. Mac OS X 自带了一个版本的 SVN, 尽管可能不是最新的。

##### 如果安装了 Xcode
只要在偏好设置 Preferences \> Downloads \> Command Line Tools \> Install 中安装即可。

##### 如果没有安装 Xcode
如果之前没有安装 Xcode, 而专门安装则有些划不来。因为 Xcode 是一个强大的集成开发环境，安装程序体积很大，有几个 G; 加之 App Store 在国内的下载速度不理想，因此如果没有开发需求，不一定要安装之。

在没有 Xcode 的情况下，只需要在苹果开发者的官网注册开发者帐号，并下载一个 Command Line Tools 的安装包，独立安装之即可。

*链接: https://developer.apple.com/downloads/index.action*

#### 在 Windows 上安装 SVN
在 Subversion 的官网下载最新的 SVN 安装包进行安装。

*链接: https://subversion.apache.org/packages.html*

安装配置完成后可以使用命令行工具，或是使用专门的 GUI 工具，如比较有名的是 TortoiseSVN. 

#### 配置过程

以下以 Mac 平台为例，讲解 SVN 的配置方法。

首先，在 Terminal 终端工具中输入 `svnserve --version` 命令查看本机 SVN 的版本以确保安装成功。

![][image-1]

接着我们新建一个目录作为我们的仓库。

	$  mkdir /your/own/path

使用 `create` 命令创建仓库。

	$ sudo svnadmin create /your/own/path

此时使用 `cd & ll` 命令进入目录/仓库查看，会发现上述 `svnadmin` 命令已经帮我们将 SVN 仓库所需要的文件生成好了，比如 **conf** 文件夹里面保存的是仓库的一些设置文件。

对 **conf** 文件夹下的 **svnserve.conf** 文件进行修改。

	[general]
	anon-access = none
	auth-access = write
	password-db = passwd
	authz-db = authz
	[sasl]

接着修改 **conf** 文件夹下的 **passwd** 文件，在 `[user]` 后面加入以下内容：

	ivan = ivanpwd
	another = userpwd

这样就设置了两个用户 ivan 和 another 以及相对应的密码，然后在 **authz** 文件中配置对应用户的权限即可。

安装配置完成后即可启动 SVN 服务。SVN 默认使用 3690 端口，如果端口未被占用，SVN 即可正常运行。

	$  sudo svnserve -d -r /your/own/path --log-file=/var/log/svn.log

### 使用方法

下面我根据我的使用体验，讲解一下 SVN 各种常用命令的使用。

1. svn checkout path (服务器地址)
	 
	SVN 有一个远程服务器上的中心版本库，我们使用 `checkout` 命令将远程服务器上的目录复制到本地，以便进行开发和修改。
	 
2. svn add files

	将新的文件加入到 SVN 的版本库里。即如果我们仅仅在开发文件夹中新增了文件，这个新增文件并不受到 SVN 版本库的管理，我们需要通过 `add` 命令将新增的文件加入到版本库中进行统一管理。

3. svn commit -m 'update message'

	`commit` 命令类似于一个快照，或者说是一个开发过程的保存，将此刻的开发状态记录下来，加入到 SVN 版本库的历史记录中。这样做，以便将来查看开发历史过程，或者是在遇到重大错误的时候，能够回滚到任何一个历史版本。
	 
4. svn update -r m path

	使用 `update` 命令将当前版本的代码更新到某一个指定的版本。
	 
5. svn status path

	使用 `status` 命令显示版本库以及子目录的状态。
	 
6. svn log path 

	使用 `log` 命令查看 SVN 仓库的日志。
	 
7. svn diff path

	SVN 作为版本控制工具，记录了每一次文件和目录的改动（以 commit 为准），因此我们可以使用 `diff` 命令将当前的状态和版本库中的文件进行对比，抑或使用此命令对比任何两个版本之间的差异。
	 
8. svn merge

	将两个版本进行合并，变为一个版本，生效于当前文件。
	 
9. svn help

	查询 SVN 的相关命令的详细说明。
	 
## CVS 和 SVN 的比较

### 存储格式

CVS 存储的是普通的文件格式，加上一些额外的信息以便版本控制的实现；SVN 的存储格式更为复杂，是基于关系数据库或二进制文件的，一方面这增加了更多更强大的功能，另一方面也使得数据，相比于前者来说，对用户不友好。

### 版本编号

CVS 中文件的版本编号是独立的，不同版本的某一个文件始终是不同的；在 SVN 下，任何一次代码的提交都会将所有文件增加到一个统一的、新的版本号，即便是 commit 中没有涉及到的文件的版本号也会被更新。如此，同一个版本的代码和文件的版本都是一致的，而不同版本号的同一个文件可能是完全相同的。

### 目录的版本控制

CVS 只能对文件进行版本控制，而不能对目录进行版本控制。因此，一个文件从一个文件夹移动到另一个文件夹中，CVS 不能进行追踪，而是分别作为“新增”和“删除”两个操作。这样的做法会导致版本库对于文件历史轨迹的追踪丢失。然而文件的“移动”、“重命名”、“复制”的操作又是极为常用的操作，这也是 SVN 渐渐取代的 CVS 的主要原因之一。

### 原子提交

上文提到，SVN 的每一次提交，都是全部文件作为一个整体提交的，这样免去了数据库损坏的风险。而 CVS 采用线性串行的批量提交，即依次一个接一个地提交文件更新。每成功提交一次文件，这个文件的新版本将会被记录到版本库中。CVS 这种做法的弊端在于，如果传输过程中出现了中断，比如网络中断，那么开发者更新的完整版本很可能没有全部提交，而是仅仅提交了部分，导致中心版本库中的文件不是一个正确的版本，或不能运行。而 SVN 提交的过程中如果发生了中断，则任何一个文件都不会被更新，这样做就能够保证中心版本库中的代码即便不是最新的，每一个版本也都是完整而正确的。

### 速度

SVN 比 CVS 快很多，但是速度带来的提升，是以更大的存储空间为代价的。

### 支持的文件类型

CVS 最初是为文本文件存储而设计的，而对其他文件类型（比如二进制文件）的支持需要手动进行复杂的设置方可使用；SVN 在此基础上支持了几乎所有的文件类型。

### 回滚

当发生意外或严重的错误的时候，CVS 允许开发者回滚到之前的任意版本，尽管可能会花费一些时间和精力。SVN 的回滚事实上是将之前的一个“好”的版本，和当前“搞砸了”的版本进行 merge 合并，而不是严格意义上的回滚至原来的版本。

---

## Git
Git 是一个免费的开源的分布式的版本控制系统 (free and open source distributed version control system). 

### 安装配置方法


#### 在 Mac 上安装 Git

我用的是 Mac OS, 首先来介绍一下如何在 Mac 上安装 Git. 

1. 在 Mac 上安装 Git 非常简单。最简单的方法就是安装 Xcode Command Line Tools. 在 Mavericks (Mac OS X 10.9) 以及以后的版本中，只要在终端 (Terminal.app) 中第一次运行 `git` 命令，如果你还没有安装这个开发者工具，终端程序便会提示你安装。

2. 上述方式安装的 Git 版本可能不是最新的，如果你想要安装最新的版本，可以在官网下载最新的安装包。

	*链接: http://git-scm.com/download/mac*

3. 另外一个简单的方式是安装 GitHub for Mac 程序。拥有图形界面的 GitHub 客户端可以让初学者很方便地完成一些常用的 Git 操作，但是有时候还是需要手动解决一些文件冲突。

#### 在 Windows 上安装 Git

我暑期实训的时候公司主要用的是 Windows 电脑，在 Windows 电脑上安装 Git 也并不繁琐。

1. 在官网下载最新的安装包进行安装。
	 
	*链接: http://git-scm.com/download/win*

2. 同样，也可以安装 GitHub for Windows 客户端。GitHub 客户端包括一个命令行版本的 Git (command line version of Git) 和一个图形化界面的程序，命令行工具可以像 Linux 和 Mac OS X 一样使用 Git 命令。拥有图形界面的 GitHub 客户端可以让初学者很方便地完成一些常用的 Git 操作，但是有时候还是需要手动解决一些文件冲突。


#### 在 Linux 上安装 Git

在 Linux 上安装 Git 则更简单，使用 Linux 的包管理工具 (package-management) 即可。

Fedora 用户使用

	$  sudo yum install git

Debian-based Linux (like Ubuntu) 用户使用

	$  sudo apt-get install git
### 使用方法

Git 十分易用，在我们的代码库（比如 my-project 文件夹）中，只要使用 

	git init

便可以在本地创建一个初始版本的库。在文件夹根目录下新建一个 *ivan.txt*, 我们可以使用

	git add ivan.txt

将这个文本文件加入到版本库中进行管理，也可以使用 

	git add --all / git add -A

将所有的新增的文件加入到版本库中。然后我们使用 

	git commit -m "Add ivan's profile text"

提交我们的更新。commit 可以理解为一个节点或者快照，以后我们可以在版本历史中看到这个时刻的代码，比如说我们在发生一些重大错误之后，可以回滚到这个时刻。

我们可以使用 `log` 命令查看全部的 commit 版本历史。

	git log

接着，我们使用 `remote add` 命令增加一个远程的地址，叫做 origin 的 remote, 连接到我的 GitHub 上叫做 **try\_git** 的项目中。

	git remote add origin https://github.com/iplus26/try_git.git

`push` 命令将我们本地的版本库 (master) 提交到远程的 remote (origin) 中。`-u` 参数告诉 Git 记住这些参数，这样我们下一次可以直接使用 `push` 命令推送我们的更新，Git 就会采用这一次的设置。

	git push -u origin master
	git push

`pull` 命令将远程代码库中的代码取回本地。

	git pull origin master
	git pull

使用 `branch` 命令新建一个分支 **ivan\_b**: 

	git branch ivan_b

使用 `checkout` 命令切换分支。

	git checkout ivan_b

在 **ivan\_b** 分支中对文件做出一些更改并提交 (commit) 之后，我们切换回 **master** 分支，将 **ivan\_b** 分支与 **master** 分支合并。

	git merge ivan_b

现在我们不需要 **ivan\_b** 分支了，我们使用 `branch -d` 命令删除这个分支。

	git branch -d ivan_b

另外，还有一些常用命令

- `status` - 查看当前版本库的状态，比如分支、文件变更等

## Git 与 SVN 的比较

### Git 是分布式的，SVN 是集中式的
Git 与其他版本控制系统（比如 SVG 和 CVS 等）的最大不同在于，Git 是分布式的 (distributed), 这意味着每一个开发者从中心版本库 (master branch) 中 check out 代码后，会在自己的机器上克隆一个自己的版本库。克隆出来的版本与被克隆的版本是完全独立的，也就是说在合并之前，开发者可以向自己的版本库中 commit 新的版本，而不会影响中心版本库。如果开发者不需要和中心版本库相连接（或者失去了连接，比如断网），即可向自己的版本库提交文件、查看历史版本记录、创建项目分支等等；等到恰当的时机，再向中心版本库提交更新即可。

分布式给开源社区的开发者带来了裨益，一个开源项目的 master 版本发布后，其他开发者可以 fork 其项目代码，增加自己的特性 feature, 或者贡献代码提交，master 分支的所有者可以选择是否合并 pull request; 当中心版本库中的代码更新的时候，各个分支的开发者可以使用 pull 命令更新自己所有库中的代码。

SVN 是典型的集中式，客户端直接对服务端进行操作；与 Git 大多数是在本地进行操作，待本地操作完成后再将内容推送到中心版本库的服务器上。

### SVN 有统一的版本号
在 SVN 下，任何一次代码的提交都会将所有文件增加到一个统一的、新的版本号，即便是 commit 中没有涉及到的文件的版本号也会被更新。如此，同一个版本的代码和文件的版本都是一致的。

### SVN 是增量存储，Git 不是
SVN 在保存文件的时候，仅仅存储的是两个版本文件之间的差异，而 Git 存储的是每个版本的快照。前者能够节省空间，后者存取文件更为方便。

### 分支

SVN 的分支仅仅是一个文件目录的 URL, 在 merge 合并分支的时候效率不高；而 Git 的分支仅仅是一个指针，Git 鼓励用户使用分支，因此无论是创建分支或是合并分支都十分便捷。

### 回滚

和 CVS 与 SVN 的区别一样，Git 支持严格意义上的回滚操作，只需要用 `revert` 命令即可回滚到之前任意一个“好”的版本，而 SVN 的回滚事实上是将之前的一个“好”的版本，和当前“搞砸了”的版本进行 merge 合并，而不是严格意义上的回滚至原来的版本。

---

## VSS
[VSS][3], 即 Microsoft Visual SourceSafe, 是微软推出的版本控制系统，一般和 Visual Studio 同时发布。

## ClearCase
[Rational ClearCase][4] 是 IBM 公司推出的软件配置管理解决方案。

---

## 官网以及参考资料

* **软件配置管理软件 - MBA 智库百科**, http://wiki.mbalib.com/wiki/软件配置管理工具
* **CVS 官网**, http://www.nongnu.org/cvs/
* **CVS - 维基百科**, https://zh.wikipedia.org/wiki/協作版本系統
* **Visual SourceSafe - 维基百科**, https://zh.wikipedia.org/wiki/Visual\_SourceSafe
* **Rational ClearCase 官网**, http://www-03.ibm.com/software/products/zh/clearcase
* **Rational ClearCase Wikipedia page**, https://en.wikipedia.org/wiki/Rational\_ClearCase
* **Apache Subversion 官网**, https://subversion.apache.org/
* **Subversion - 维基百科**, https://zh.wikipedia.org/zh/Subversion
* **Subversion 与版本控制**, http://svnbook.red-bean.com/index.zh.html
* **TortoiseSVN 官网**, tortoisesvn.net/
* **Git 官网**, https://git-scm.com/
* **GitHub 官方 Git 教程**, https://try.github.io

[1]:	http://www.nongnu.org/cvs/
[2]:	http://ftp.gnu.org/non-gnu/cvs
[3]:	https://msdn.microsoft.com/library/ms181038(en-us,vs.80%5C).aspx
[4]:	http://www-03.ibm.com/software/products/zh/clearcase

[image-1]:	svnserve-version.png