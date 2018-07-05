
git 的使用

	
https://gitee.com/progit/1-%E8%B5%B7%E6%AD%A5.html#1.1-%E5%85%B3%E4%BA%8E%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6

1.3. git基础
	更像是把变化的文件作快照后，记录在一个微型的文件系统中。每次提交更新时，它会纵览一遍所有文件的指纹信息
	并对文件作一快照，然后保存一个指向这次快照的索引。为提高性能，若文件没有变化，Git 不会再次保存，
	而只对上次保存的快照作一链接。
	
	1.3.1.快照记录，而非差异比较
		git记录的是文件的快照信息。若文件没有改变，直接保存一个指向快照的索引
	
	1.3.2.操作几乎在本地
		git的绝大操作都只需要访问本地文件和资源。因为 Git 在本地磁盘上就保存着所有当前项目的历史更新，所以处理起来速度飞快。
		如果想要看当前版本的文件和一个月前的版本之间有何差异，Git 会取出一个月前的快照和当前文件作一次差异运算，而不用请求远程服务器来做这件事
				
		
	1.3.3.保存数据的完整性	
		在保存到Git之前，所有的数据都要进行内容的校验和（checksum）计算。使用的是SHA-1算法，通过文件的内容或目录的结构计算出一个SHA-1的hash值。
		作为指纹字符串。该字符串由40个16进制组成。24b9da6552252987aa493b52f8696cd6d3b00373
		Git 的工作完全依赖于这类指纹字串，所以你会经常看到这样的哈希值。实际上，所有保存在 Git 数据库中的东西都是用此哈希值来作索引的，而不是靠文件名。
		
	1.3.4多数操作仅添加数据	
		在 Git 里，一旦提交快照之后就完全不用担心丢失数据
		
	1.3.5文件的三种状态
		在Git内，文件只有三种状态：已提交（committed），已修改（modified）和已暂存（staged）
			已提交：表示该文件已经被安全地保存在本地数据库中了。
			已修改：表示修改了某个文件，但还没有提交保存。
			已暂存：表示把已修改的文件放在下次提交时要保存的清单中。
			
		由此我们看到 Git 管理项目时，文件流转的三个工作区域：Git 的工作目录，暂存区域，以及本地仓库。
		
										Local Operations
			
				工作目录					  暂存区域						本地仓库
			working directory				staging area			git directory(repository)
					|							|								|	
					|							|								|
					|<--------------------checkout the project-------------------
					|							|								|
					|							|								|
					|--------stage files------->|								|
					|							|								|
					|							|-------------commit----------->|
					|							|								|

		1.本地仓库:每个项目都有一个 Git 目录（译注：如果 git clone 出来的话，就是其中 .git 的目录；如果 git clone --bare 的话，
				新建的目录本身就是 Git 目录。），它是 Git 用来保存元数据和对象数据库的地方。该目录非常重要，每次克隆镜像仓库的时候，
				实际拷贝的就是这个目录里面的数据。
		
		2.工作目录:从项目中取出某个版本的所有文件和目录，用以开始后续工作的叫做工作目录。这些文件实际上都是从 Git 目录中的压缩对象数据库中提取出来的，接下来就可以在工作目录中对这些文件进行编辑。

		3.暂存区域:所谓的暂存区域只不过是个简单的文件，一般都放在 Git 目录中。有时候人们会把这个文件叫做索引文件，不过标准说法还是叫暂存区域。

		基本的 Git 工作流程如下：
			1.在工作目录中修改某些文件。
			2.对修改后的文件进行快照，然后保存到暂存区域。
			3.提交更新，将保存在暂存区域的文件快照永久转储到 Git 目录中。
			所以，我们可以从文件所处的位置来判断状态：如果是 Git 目录中保存着的特定版本文件，就属于已提交状态；如果作了修改并已放入暂存区域，
			就属于已暂存状态；如果自上次取出后，作了修改但还没有放到暂存区域，就是已修改状态。到第二章的时候，我们会进一步了解其中细节，
			并学会如何根据文件状态实施后续操作，以及怎样跳过暂存直接提交。
				
		
	
1.5.安装后的配置
	git config
		用户信息 ****
		第一个要配置的是你个人的用户名称和电子邮件地址。这两条配置很重要，每次 Git 提交时都会引用这两条信息，说明是谁提交了更新，所以会随更新内容一起被永久纳入历史记录：
		$ git config --global user.name "John Doe"
		$ git config --global user.email johndoe@example.com
		
		如果用了 --global 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。
		如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。
		
		
	文本编辑器
		接下来要设置的是默认使用的文本编辑器。Git 需要你输入一些额外消息的时候，会自动调用一个外部文本编辑器给你用。默认会使用操作系统指定的默认编辑器，一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：
		$ git config --global core.editor emacs
	差异分析工具
		还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话：
		$ git config --global merge.tool vimdiff
		
	
	查看配置信息
		git config --list
		有时候会看到重复的变量名，那就说明它们来自不同的配置文件（比如 /etc/gitconfig 和 ~/.gitconfig），不过最终 Git 实际采用的是最后一个。
		也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可，像这样：
			$ git config user.name
			
	
		
1.6.获取帮助
		$ git help <verb>
		$ git <verb> --help
		$ man git-<verb>
		比如，要学习 config 命令可以怎么用，运行：
		$ git help config
		
		
2.0.取得项目的git仓库
	有两种取得 Git 项目仓库的方法。第一种是在现存的目录下，通过导入所有文件来创建新的 Git 仓库。第二种是从已有的 Git 仓库克隆出一个新的镜像仓库来。
	2.1.1在工作目录中初始化新仓库
		要对现有的某个项目开始用 Git 管理，只需到此项目所在的目录，执行：
			$ git init
		如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：
			$ git add *.c
			$ git add README
			$ git commit -m 'initial project version'
	2.1.2从现有仓库克隆
		克隆仓库的命令格式为 git clone [url]。比如，要克隆 Ruby 语言的 Git 代码仓库 Grit，可以用下面的命令：
		$ git clone git://github.com/schacon/grit.git
		这会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录，然后从中取出最新版本的文件拷贝。如果进入这个新建的 grit 目录，你会看到项目中的所有文件已经在里边了，准备好后续的开发和使用。如果希望在克隆的时候，自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：
		$ git clone git://github.com/schacon/grit.git mygrit
		唯一的差别就是，现在新建的目录成了 mygrit，其他的都和上边的一样。
	
		

		
	
	2.2.记录每次更新到仓库
		工作目录下面的所有文件都不外乎这两种状态：已跟踪或未跟踪。
							File	Status	Lifecycle
		untracked				unmodifief			modified			staged
			|              			|                	|    	 		   | 															
			|              			|                	|    	 		   | 															
			|              			|--edit the file--->|    	 		   |  															
			|-----add the file----->|                	|    	 		   | 															
			|              			|                	|    	 		   | 															
			|<--remove the file---->|            		|-stage the file-->|													
			|              			|                	|    	 		   | 															
			|              			|                	|    	 		   | 															
			|              			|<----------------commit---------------|												
			|              			|                	|    	 		   |								
		2.2.1.检查文件状态
			git status
		2.2.2.跟踪新文件
			git	add 【file】
		2.2.3.暂存已修改的文件
			git add 【file】
		2.2.4.忽略文件
			cat	.gitignore
			# 此为注释 – 将被 Git 忽略
				# 忽略所有 .a 结尾的文件
				*.a
				# 但 lib.a 除外
				!lib.a
				# 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
				/TODO
				# 忽略 build/ 目录下的所有文件
				build/
				# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
				doc/*.txt									...*/												
		2.2.5.查看已暂存和未暂存的更新
			git diff
		
	2.4.修改
		2.4.1.撤销已经暂存的文件
			1).git add <file>
			2).git status
			3).git reset HEAD <file>
		2.4.2.取消对文件的修改
			git checout -- <file>
			
	2.5.远程仓库的使用
		2.5.1.查看当前的远程仓库
			git remote
			git remote -v
		2.5.2.添加远程仓库
			git remote add pb git://git.../....git  （pb 相当于取名字）
			git remote -v
			现在可以用字符串 pb 指代对应的仓库地址了  
			现在要抓取所有 Paul 有的，但本地仓库没有的信息，可以运行 git fetch pb：
			git fetch pb
		2.5.3.从仓库抓取数据
			git fetch [remote-name]
		2.5.4.数据推送
			git push origin master
		2.5.5.查看远程仓库
			git remote show origin
		2.5.6.远程仓库的删除和重命名
			git remote rename pb paul
			对远程仓库的重命名，也会使对应的分支名称发生变化，原来的 pb/master 分支现在成了 paul/master。
			碰到远端仓库服务器迁移，或者原来的克隆镜像不再使用，又或者某个参与者不再贡献代码，那么需要移除对应的远端仓库，可以运行 git remote rm 命令：
			git remote rm paul
	
	2.6.打标签
		2.6.1.列显出已有的标签
			git tag
		2.6.2.新建标签
			Git 使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。轻量级标签就像是个不会变化
			的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，
			它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy
			Guard (GPG) 来签署或验证。一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性
			加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。
		2.6.3.含附注的标签
			git tag -a v1.4 -m 'my version 1.4'
		2.6.4.签署标签
			git tag -s v1.5 -m 'my signed 1.5 tag'
		2.6.5.轻量级标签
			git tag v1.4-lw
			git show v1.4-lw
		2.6.6.验证标签 git tag -v [tag-name]
			git tag -v v1.4
		2.6.7.后期加注标签
			1).git log --pretty=oneline
			2).git tag -a v1.2 9fceb02
			
				<<<<<<
					zl@lll MINGW64 /d/gitStudy/girl (dev)
					$ git log --pretty=oneline
					e918249c434e744c355af72b4d5f2ba2b27a261e all
					15886934b906fc6765a07ec35de0fdb15b9f4224 Merge branch 'dev' of github.com:tiamocc/girl into dev
					e3be7b8406682703346b23ae1464dcaf5ee4aa84 new file one.java hello,update
					18cef3d6e3f37835bd93498f5e25a83009a48b6f new file one.java
					3902ba534624f32bb826dd74b49dcc98d96590e7 dskjkjskj
					854192051ac34440ba0d06a8737032b415b66acb djksksk
					7df3d84ce5b5e40e23c649b019e3ff2081b1593f ahajj
					606966dcf0c7f4e6c88ec4c3872bdf1e15daefac 提交d
					7330ff9c25ebd9424e591775f6bd8cb1c0cd4b1c 提交c
					357d97226c90dc8157679da240135342693bb224 提交，测试commit回滚
					77d5ed2da10fa399039a4ceb0d9147fb06e45666 initial project version
					737fd1dcb66036821658e4a4bc15dcb62148d7cc Initial commit

					zl@lll MINGW64 /d/gitStudy/girl (dev)
					$ git tag
					v1.4
					v1.4-lw

					zl@lll MINGW64 /d/gitStudy/girl (dev)
					$ git tag -a v1.1 606966dcf0c7f4e6c88ec4c3872bdf1e15daefac

					zl@lll MINGW64 /d/gitStudy/girl (dev)
					$ git tag
					v1.1
					v1.4
					v1.4-lw
				
				>>>>>
		2.6.8.分享标签
			默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。其命令格式如同推送分支，运行 git push origin [tagname] 即可：
			git push origin [tagname]
			git push origin v1.4
			如果要一次推送所有本地新增的标签上去，可以使用 --tags 选项：
			git push origin --tags
			
	2.7.技巧和窍门
		2.7.1.自动补齐
		2.7.2。Git命令别名
			Git 并不会推断你输入的几个字符将会是哪条命令，不过如果想偷懒，少敲几个命令的字符，可以用 git config 为命令设置别名。来看看下面的例子：
			$ git config --global alias.co checkout
				$ git config --global alias.br branch
				$ git config --global alias.ci commit
				$ git config --global alias.st status
			现在，如果要输入 git commit 只需键入 git ci 即可。而随着 Git 使用的深入，会有很多经常要用到的命令，遇到这种情况，不妨建个别名提高效率。

			使用这种技术还可以创造出新的命令，比方说取消暂存文件时的输入比较繁琐，可以自己设置一下：

			$ git config --global alias.unstage 'reset HEAD --'
			这样一来，下面的两条命令完全等同：

			$ git unstage fileA
				$ git reset HEAD fileA
			显然，使用别名的方式看起来更清楚。另外，我们还经常设置 last 命令：

			$ git config --global alias.last 'log -1 HEAD'
			然后要看最后一次的提交信息，就变得简单多了：

			$ git last
				commit 66938dae3329c7aebe598c2246a8e6af90d04646
				Author: Josh Goebel <dreamer3@example.com>
				Date: Tue Aug 26 19:48:51 2008 +0800

				test for current head

				Signed-off-by: Scott Chacon <schacon@example.com>
			可以看出，实际上 Git 只是简单地在命令中替换了你设置的别名。不过有时候我们希望运行某个外部命令，而非 Git 的子命令，这个好办，只需要在命令前加上 ! 就行。如果你自己写了些处理 Git 仓库信息的脚本的话，就可以用这种技术包装起来。作为演示，我们可以设置用 git visual 启动 gitk：

			$ git config --global alias.visual '!gitk'
			
				
3.GIT分支
	3.1.何谓分支
		
	3.2.分支的新建与合并
	3.3.分支的管理
	3.4.利用分支进行开发的工作流程
	3.5.远程分支
	3.6.分支的衍合
	3.7.小结
			
			
==========================================================================================			
	注意： 
		git psuh origin :dev //删除远程dev分支
		git psuh origin dev :dev //删除远程dev分支*********************
		git psuh origin test:dev //本地test分支作为远程dev分支**********************
		git push origin master 	 //当前分支重新创建远程master分支
		
********git 拉取远程分支到本地
			1.	git checkout -b 本地分支名x origin/远程分支名x
			2.	git fetch origin 远程分支名x:本地分支名x
				git checkout 本地分支名x
				
********git 新建本地分支并上传到远程


********git 将本地分支作为远程分支
			 git push origin dev:master //将本地分支dev 作为远程master分支
			 git push origin dev:dev // 提交本地dev分支作为远程的dev分支
********git 删除远程分支
			 $ git push origin :dev // 删除远程的dev分支
			 
【本地代码库回滚】：
		git reset --hard commit-id :回滚到commit-id，讲commit-id之后提交的commit都去除
		git reset --hard HEAD~3：将最近3次的提交回滚	
		//git reset --hard 357d97226c90dc8157679da240135342693bb224
		
【远程代码库回滚】：二种方式
		原理：先备份本地分支和远程分支，将本地分支退回到某个commit，删除远程分支，再重新push本地分支
		1、新建backup分支 作为备份，以防万一
			git branch backup 
		2、将本地的backup分支　推送到远程的backup
			git push origin backup:backup 
		3、本地仓库彻底回退到xxxxx版本，xxxxx版本之后的commit信息将丢失
			git reset --hard xxxxx 
				
				一：4、删除远程的master分支 (注意master前有个:)
						git push origin :master
						主要远程仓库的master如果是保护分支将报错，请去掉对分支的保护设置：
						remote: GitLab: You are  allowed  to  deleted protected branches from this project. To http://gitlab.mogujie.org/shihao/afanty.git ! [remote rejected] master (pre-receive hook declined) error: failed to push some refs to 'http://gitlab.mogujie.org/xxxx/xxxx.git'
				一：5、重新创建远程master分支(这跟第１次提交本地代码库给远程仓库的命令一样)
						git push origin master 
				
				二：4、加入-f参数，强制提交，远程端将强制跟新到reset版本
						git push -f origin master 
				
	
	git log
	git log -p -2
	git log -U1 --word-diff		//	单词
	git log --stat				// 	行
	git log --pretty=oneline
	git log --pretty=short
	git log --pretty=full
	git log --pretty=fuller 
	git log --pretty=format:"%h - %an, %ar : %s"
	git log --pretty=format:"%h %s" --graph
	git log --since=2.weeks

		
		
		git 删除文件
			rm 文件
			git status
			git rm 文件
			git commit
			
		git 误删文件
			rm 文件 （这是误删操作）
			git status
				   显示： deleted:    Application/Home/View/Index/Index.html
			git reset HEAD Application/Home/View/Index/Index.html
			git checkout   Application/Home/View/Index/Index.html
			
		重命名git本地分支
			Git branch -m old_local_branch_name new_local_branch_name

		重命名git远程分支
			Step1：重命名远程分支对应的本地分支
				git branch -m old_local_branch_name new_local_branch_name

			step2：删除远程分支
				git push origin :old_local_branch_name

			step3：重新推送新命名的本地分支
				git push origin new_local_branch_name
			
		
		
		
		
		
		
		







