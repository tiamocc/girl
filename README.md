
git 的使用

第二章节
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
			git remote -v  (verboses)
		2.5.2.添加一个远程仓库
			git remote add [shortname] [url]

		git remote add pb git //github.com/../...git
		现在可以用字符串 pb 指代对应的仓库地址了。比如说，要抓取所有 Paul 有的，但本地仓库没有的信息，可以运行 git fetch pb：
		git fetch pb
		现在，Paul 的主干分支（master）已经完全可以在本地访问了，对应的名字是 pb/master，你可以将它合并到自己的某个分支，或者切换到这个分支，看看有些什么有趣的更新。









