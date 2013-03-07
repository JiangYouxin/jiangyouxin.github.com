---
layout: post
title: git merge --squash的用法
meta: 2013.3.7, 北京
---

将`feature`（分支）合并到`master`（主干），并推送到服务器上，正常的命令序列为：

	git checkout master
	git merge feature
	# 在这里处理冲突
	git commit
	git push

这个时候我们向服务器推送的是一个Merge结点，它有两个父结点，同时保留了分支和主干的历史。

但有时，没有权限向服务器推送Merge结点，或者没有必要把分支的历史保留得太细。这时可以新开一个分支做合并，然后把整个分支压缩（squash）为主干上的***一个***提交：

	git checkout master -b work
	git merge feature
	# 在这里处理冲突
	git commit
	# 以下压缩为一个提交再推送到服务器
	git checkout master
	git merge work --squash --ff-only
	git commit
	git push
	# 擦屁股
	git branch -d work

也可以在第一次合并时直接使用--squash参数，但这样生成的commit message会少一次合并的信息：

	git checkout master
	git merge feature --squash
	# 在这里处理冲突
	git commit
	git push

另外，用这种方法合并后，本地和服务器的分支都最好删除掉：

	git branch -D feature
	git push origin :feature
