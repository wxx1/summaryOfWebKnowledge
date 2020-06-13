## git

Git是目前世界上最先进的分布式版本控制系统。

工作区：

版本库(.git文件)：包含包含暂存区和master分支

![](..\img\git.jpg)

#### 1、SVN和Git的区别

​	SVN时集中式版本控制系统

#### 2、git创建仓库

```bash
git init //初始化
git add .//推送所有修改到暂存区
git commit -m 'xxx' //将暂存区所有修改提交到本地仓库
```

#### 3、版本回退

```bash
git reset --hard commit_id
```

使用`git log`可以查看提交历史，以便确定要回退到哪个版本

使用`git reflog`查看命令历史，以便确定回到未来哪个版本

#### 4、远程库

- 本地仓库和远程仓库之间是通过SSH加密的，所有当你第一次连接远程库之前应该先将自己的公钥放在远程服务器中，这样你就可以向远程库推送

```bash
git remote add origin xxxx.git//关联一个远程库
git push -u origin master//第一次向远程库推送，远程库会创建master，并将其与本地master连接起来

```

#### 5、分支管理

查看分支：```git branch```

创建分支：`git branch <name>`

切换分支：`git checkout <naem>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

查看有分支形式的提交历史：` git log --graph --pretty=oneline --abbrev-commit`



#### 6、解决冲突

- 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交合并完成
- 解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交



#### 7、git分支管理策略

- git的分支操作并不是生成现有代码的物理拷贝，而是**生成一个指向当前版本的指针**

- 快进式合并（fast-farward merge）

  - git合并两个分支时，如果顺着一个分支走下去可以到达另一个分支的话，那么git在合并两者时，只会简单的把指针右移，叫做“快进”，这样合并简单、快速。
  - 但是如果将开发分支删除后，在master主分支上看不到任何合并的历史，其次快进式合并会将开发分支上feature的提交历史混入到master，搅乱master的提交历史，因为master分支是主分支，要尽量保证其是稳定版本的迭代，使用快进式合并，如果master主分支上出现问题，需要回退到上个版本时，可能会回退到开发分支的历史中，因为开发分支上的历史也合并进入了master里，这非常不可理
  - **解决**：使用`--no--ff`禁止快进式合并，此时提交会产生一个心的提交，是将上是对开发分支进行一个复制，创建了一个新的提交

  ![](..\img\merge.png)

- 合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并

#### 8、`stash`—将当前工作区“储藏”起来

- 当你在某个分支上工作进行到一般，还没提交，此时需要临时处理一个bug，此时可以使用`stash`功能，将工作区储藏起来

- `git stash` 将当前工作现场储藏起来，等以后恢复现场再继续工作

- 解决问题，如再master分支上临时创建新分支,修复bug，提交，之后在master上合并，删除bug分支

  ```bash
  git checkout master
  git checkout -b fix-bug-001
  
  git add .
  git commit -m '修复bug'
  
  git checkout master
  git merge --no--ff -m '合并fix-bug-001' fix-bug-001
  
  ```
  
- 此时可以回到dev分支继续干活 ``git checkout dev``

- `git status` 工作区是干净的，那刚才的工作现场去哪了

- `git stash list` 查看工作现场还在，知识被存在了某个地方

- `git stash pop` 恢复工作区同时删除stash内容

- 之后就可以接着之前的工作继续完成

#### 9、多人协作

- 查看远程库信息`git remote` -v
- 克隆远程库默认克隆`master`分支
- 创建本地分支并关联到远程分支`git checkout -b dev origin/dev`
- 当你的小伙伴向origin/dev推送了自己的提交，而碰巧你也在该分支对同样文件作了修改，并尝试推送，推送失败，原因会产生冲突。解决：先`git pull`把最新的origin。dev抓下来，在本地合并，解决冲突，然后再推送
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；



#### 10、标签管理

- tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起

- 默认标签是打在最新提交的commit上的。

- 如果想给之前提交的代码打标签，先找到历史提交，查看commit id，之后打上标签就可以了

  ​	`git tag <tagName>  <commit id>`

- 标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。 

```bash
git tag v1.0

$ git tag #查看tag
v1.0

$ git show v0.1 #可以看到说明文字
```

- 如果标签打错了，也可以删除， 因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以本地安全删除
  - ```git tag -d v0.9```
- 将某个标签推送到远程
  - `git push origin <tagname>`
  - 或者一次性推送` git push origin --tags`
- 命令`git push origin :refs/tags/`可以删除一个远程标签。



#### 11、忽略特殊文件

- 创建`.gitignore`文件，将要忽略的文件名填进去，Git会自动忽略这些文件