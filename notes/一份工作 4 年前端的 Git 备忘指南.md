> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6967634683811069982)

> 刚毕业的时候用过极短时间的 SVN，后面就一直在用 Git 来做代码的版本控制了，前前后后差不多 4 年的时间，期间做了一些在使用 Git 过程中的记录和心得，在这里分享给大家，大家或许可以从中吸收到一些有用的东西

刚毕业的时候用过极短时间的 SVN，后面就一直在用 Git 来做代码的版本控制了，前前后后差不多 4 年的时间，期间做了一些在使用 Git 过程中的记录和心得，在这里分享给大家，大家或许可以从中吸收到一些有用的东西。

无论是 github，还是 gitlab，还是其他的代码托管平台，代码管理都是用 git 去做的，git 可以说是一名程序员的必备技能，对于工作和面试都是非常有帮助的。

*   Git 常用命令
*   优雅使用 Git 的一些实践
*   oh-my-zsh 常用命令

### Git 常用命令

*   git 克隆远程分支仓库：

`git clone -b 分支名称 远程地址`

git 克隆远程仓库项目时如果不指定分支，只会克隆默认分支的内容。

*   查看 git 用户名和邮箱

```
git config user.name
git config user.email
复制代码
```

*   分支相关

```
git branch（查看当前分支)
git branch -a（查看所有分支）
git checkout 分支名（切换到对应分支） 会自动将代码更新为分支代码
git branch 分支名（创建一个分支）
git branch -d 分支名（删除一个分支）
git branch -D 分支名（强制删除一个未合并的分支）
git checkout -b 分支名 [基于的分支名或commit值]（切换分支并直接切换过去）
复制代码
```

*   查看 git 历史

`history`

*   按照关键词搜索 git 历史

`history | grep push`

*   查看 commit 历史

```
git log
git log --summary
复制代码
```

*   设置 git 账号

```
git config --global user.name "foo"
git config --global user.email foo@gmail.com"
git config user.name "foo"
git config user.email "foo@gmail.com"
复制代码
```

*   查看 git 账号

```
git config --global --list
git config --local --list
复制代码
```

*   仅仅查看某一项的配置

`git config --local user.name`

*   回滚本次修改

```
git reset HEAD foo.js
git checkout -- foo.js
复制代码
```

*   查看本次修改的代码

```
git diff
git diff HEAD
git diff --staged
复制代码
```

*   提交后发现丢了几个文件没有提交

```
发现丢了修改记录，重新添加
git add "*.html"
重新提交，最终只有一个提交
git commit --amend
复制代码
```

*   缓存某种后缀的文件

`git add "*.js"`

*   清除缓存区中的文件

`git reset octofamily/octodog.txt`

*   彻底删除某种后缀的文件

`git rm "*.txt"`

*   合并分支到 master

`git merge 分支名`

*   add . 之前取消提交某些文件

`git checkout -- <filename>`

*   藏代码到脏目录（适用于其他成员修改了相同分支代码，但又不想提交）

`git stash`

*   释放脏目录代码

`git stash pop`

*   释放指定脏目录代码

`git stash pop stash@{0}`

*   删除远程分支 (此分支必须是非默认分支)

`git push origin --delete branchname`

*   已经 commit，强制回退到旧版本

```
git log//找到commit hash值
git reset --hard hash值
复制代码
```

*   查看 stash 目录

`git stash list`

*   删除某一个 stash

`git stash drop stash@{0}`

*   设置远程仓库地址

`git remote set-url origin git@foo.bar.com:baz/helloworld.git`

*   本地创建了新分支，但是 orgin 没有，push 代码前

`git push --set-upstream origin preproduction`

*   指定 tag 到远程

`git push origin <tag_name>`

*   将全部 tag 打到远程

`git push --tags`

*   查看当前 tags

`git tag --list`

*   仅仅删除 index 不删除 working tree 上的. idea 文件

`git rm --cached -r .idea` // --cached 仅仅删除 index，-r（recursive）递归删除. idea 目录下的所有文件

*   git 主动 track 文件，控制文件，做好提交准备

`git add <file(s)>/.`

*   git unstage 文件，释放文件，选择性控制

`git reset HEAD <file(s)>/.`

*   暂存区文件如何覆盖工作目录文件

`git reset HEAD <file(s)>/. && git checkout -- <file(s)>/.`

*   提交已经被 git 管理的，modified 为红色的所有文件

`git add -u`

*   重置工作区和暂存区的所有文件为原始状态

`git reset --hard`

*   比较当前分支与某次提交的区别

`git diff HEAD [commit hash fragment]`

*   删除一个分支

`git branch -D [branch name]`

*   同步到 remote 后，合并多个 commit 为 1 个

```
git rebase -i HEAD~2/hash
pick && squash
:wq!
复制代码
```

*   查看项目的 origin 代表的地址

`git remote -v`

*   pick 中途误退出

`git rebase --abort`

*   未同步到 remote，重新提交

`reset soft`

*   gitflow release 发布新版本

```
git flow release start v0.5.0
npm version minor
git flow release finish -n
git push
git checkout master
git push
复制代码
```

*   gitflow hotfix 修复一个 master 上的 bug

```
git flow hotfix start foo
npm version patch // 注意：一定要在修复bug代码之前新增版本号
git add .
git commit -m "version change message"
git flow hotfix finish -n
git push
git checkout master
git push
复制代码
```

*   对比 2 个分支的日志

```
git log develop..master
复制代码
```

*   合并 master 代码到 feature

```
git checkout feature
git merge master
复制代码
```

```
git merge master feature
复制代码
```

*   git rebase 冲突时怎么办

```
resolve conficts
git add .
git rebase --continue
复制代码
```

*   squash 多个 commits 成一个怎么敲？

假设 merge feature 到 master。

```
git checkout master
git merge --squash feature
git commit -m "这是一次squash commit"
git push
复制代码
```

*   查看当前分支的父分支

```
git reflog show <childBranch>
32c3956 (HEAD -> currentBranch, origin/fatherBranch, fatherBranch, list) childBranch@{0}: branch: Created from fatherBranch
复制代码
```

childBranch 是你新建的分支。 fatherBranch 是它的父分支，也就是来源分支。

*   撤销远程分支错误提交

```
...reset
git push --force
复制代码
```

其实使用本地分支的提交替代远程分支。

*   误删除领先远程的本地分支如何恢复？

```
git reflog // 找出最新的commit sha1值，HEAD@{1}比HEAD@{2}新
git branch branchName <sha1>
复制代码
```

通过 git reflog 找到一个 commit，然后再 cherry-pick 也可以。

*   删除由 npm version patch/minor/major 误添加的 tag

```
git tag | grep v1.1.38
git tag -d v1.1.38
git push origin :refs/tags/v1.1.38
复制代码
```

*   git fetch 与 git pull 的区别

```
git fetch 更新origin/*下的所有分支，在发布git flow feature前很有用，用于更新remote分支。
git pull 主要用来更新多人合作的当前分支，多用于更新local分支。
// https://www.git-tower.com/learn/git/faq/difference-between-git-fetch-git-pull
复制代码
```

*   远程分支删除，本地 git fetch 不能更新到最新分支

```
git fetch --prune

--prune什么意思？
Before fetching, remove any remote-tracking references that no longer exist on the remote.
复制代码
```

*   及时查看本地所有分支的状态

```
git remote show origin
复制代码
```

*   假如远程已不存在这个分支，git fetch --prune 也更新不到状态，该怎么办？

```
git push origin --delete feature/fix-chat-unread-msg-async
复制代码
```

*   删除单个脱离的远程分支

```
git remote prune <name>
复制代码
```

*   取消一次合并（merge）

`git merge --abort`

*   回退一次 commit

`git revert Head/[commit hash]`

### 优雅使用 Git 的一些实践

*   windows 下 gitbash 支持中文输入：

```
1）鼠标左键点击左上角git的logo
2）找到options并且切换到text目录，将Character set设置为UTF-8
复制代码
```

*   生成 ssh-key

`ssh-keygen -t rsa -C "gaokai20100801@qq.com"`

*   windows 查看 ssh-key

`/c/Users/frank/.ssh/id_rsa.pub`

*   mac/linux 查看 ssh-key

`cd ~/.ssh` `ls` `cat id_rsa.pub`

*   git flow

[danielkummer.github.io/git-flow-ch…](https://danielkummer.github.io/git-flow-cheatsheet/index.html)

*   误删除 stash，该怎么办？

```
git fsck --unreachable |
grep commit | cut -d\  -f3 |
xargs git log --merges --no-walk --grep=WIP
复制代码
```

找到对应的 commit hash 值

```
git stash apply 1f55da93d26cd51f15f9e93351dae6b75e25e36f
复制代码
```

*   .idea 修改总是会提醒，.gitignore 不生效

`.idea/`

```
git rm -r --cached .idea
复制代码
```

*   Git 中的 origin 是什么？

origin 是一个变量，代表着一个 git 仓库地址。可以使用`git remote -v`查看 origin 代表的地址。

*   Git 的 system，global 和 local 参数分别代表什么？

```
--system可以输出很多git的系统设置，其中最有用的是alias，例如git s代表了git status，系统所有登录用户有用。
--global输出了git的全局设置，主要包括全局的user.name和user.email，优先级低于单个仓库中设置的user.name和user.email，当前用户所有仓库有用。
--local输出了git的项目设置，主要包括remote.origin.url以及gitflow的很多配置，只对某个仓库有用。
复制代码
```

*   Git 工作区和暂存区的区别？

```
add           commit
工作目录---->暂存区---->版本历史
复制代码
```

```
暂存区：git已经获得了对文件的管理权限，暂存区文件有状态：new file，deleted，modified等等。
版本历史：git log查看每一次commit记录。
意外收获：
若是想非常细粒度的控制commit记录，可以使用git add 指定文件，分开多次commit，每一次commit提交一个细粒度功能的变更文件集合，多次走文件目录 暂存区 版本历史这个流程。
复制代码
```

*   Git 如何重命名文件？

```
git mv README.md readme.md
复制代码
```

*   Git 的 working tree 和 index 是什么意思？

```
index指的是git索引，可以理解成git有文件的一个复制，仅删除index则仅删除存在于git中的文件。
working tree则是指操作系统的工作树，也就是操作系统的磁盘上存储的文件。
举两个常用的例子：
1. 仅删除git index中的文件，.idea等IDE隐藏的工作树文件是不能删除的：**--cached**
git rm --cached -r .idea // **--cached仅仅删除index**，-r（recursive）递归删除.idea目录下的所有文件
2. 删除index和working tree上的文件，恩断义绝

git rm 删除index上和working tree上的文件，
仅仅删除working tree不删除index的情况，不存在。
复制代码
```

*   nothing to commit 和 working tree clean?

```
暂存区没有可以提交到版本历史的内容。
工作区也是干净的。
复制代码
```

*   如何一目了然地区分出工作区和暂存区？
    *   **Your branch is ahead of 'origin/master' by 1 commit** 版本历史
    *   **Changes to be committed** 暂存区
    *   **Untracked files** 工作区

```
// 版本历史
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
// 暂存区
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        renamed:    readme.md -> README.md
        new file:   helloman

// 工作区
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        hi
复制代码
```

*   如何更加优雅地查看日志？

```
git log --oneline 简洁的commit记录
git log -n2 --oneline 最近的2次简洁的commit记录
git log --all 所有分支的历史版本信息
git log --graph 图形化查看版本演进历史
git log --oneline --all -n4 --graph 组合查看日志
复制代码
```

*   如何快速定位到 git 的命令文档？

`git help --web log` 浏览器查看`git log`的用法

*   git 自带的图形化界面怎么看？

`gitk` 无需安装第三方插件，在纯命令行下，无第三方软件情况下可用。

*   git 里的作者和提交人不一样吗？

作者是代码的生成者，是为了版权保护。

*   神秘的. git 目录

```
HEAD 工作分支refs/heads/foo
config repo的配置信息
refs heads，分支；tags，标签或者里程碑
refs/heads/master 存放了什么，最新的一个commit
refs/tags/js01 存放了什么，最新的一个tag，包含一个object
objects 文件夹，2个字符的和松散的pack文件夹，存放的是tree，tree下有blob文件
复制代码
```

可以直接通过 vim 修改 HEAD，config 等信息，和命令的作用是相同的。

*   如何判断 git 文件的类型？

`git cat-file -t/-p [hash fragment]` // -t 类型，-p 内容 只要任何文件的文件内容相同，在 git 眼里，它就是唯一的一个 blob。

```
commit  
tree // 位于objects目录下
blob // 位于objects目录的二级目录下，具体的文件
复制代码
```

*   tree, commit, blob 的区别？

```
commit：一个commit肯定会对应一棵树，包含了根tree，author，committer，parent等等一个commit对象的信息。
tree：取出一个commit，存放了一个快照，这个快照，对应了当前项目的所有的文件夹及其文件的快照，是特定时间的整个仓库的一个状态；树里可以有blob，也可以有树，因为树是文件夹；根树是最大的树。
blob:  与文件名是否相同无关，只要内容相同，就是唯一的blob。
复制代码
```

*   一个 commit 包含了哪些？

```
git cat-file -p [commit hash fragment]
复制代码
```

包含 tree，parent，author 和 commiter。

```
tree f06f7f36af17cb9098031c66d22a7910c0fa1bac
parent 92a55c8a5b1d38d224232ad84b9b728ae77189cb
parent eda632a1f2a3ea049c5f5268f6b2f064b71898ce
author FrankKai <gaokai20100801@gmail.com> 1548139120 +0800
committer FrankKai <gaokai20100801@gmail.com> 1548139120 +0800

Merge branch 'feature/chatBreakChange' into prerelease

# Conflicts:
#       src/api/chat.js
复制代码
```

*   一个 tree 包含了哪些？

```
git cat-file -p [tree hash fragment]
复制代码
```

包含 tree，blob。

```
100644 blob 015aaf344153ed7822069b2a98898b7d7a215b0d    .babelrc
100644 blob 9d08a1a828a3bd2d60de3952744df29f9add27fa    .editorconfig
100644 blob 080140b833db5b758b1eb869a269f4bbb068a19e    .eslintignore
100644 blob 8f777c376c0314e480f9bbba273d4902810bcb11    .eslintrc.js
100644 blob 895e844218637929546ed2295ae90f991ceb5d38    .gitignore
100644 blob db7b635d23657349dbe4c33cc353ef4efd8ca960    .npmrc
100644 blob 797e871f4b8c0a3071e8b6ab2cc40b804cd2971c    .postcssrc.js
100644 blob d3983c1d6a5525aae58b823448723434ca83ceed    .prettierrc
100644 blob 93cc7473ab066204f3329221111a945e2dc83576    BUS.md
100644 blob defc3d9914d1af08e6670b96995261bfe1fb61a6    CHANGELOG.md
100644 blob bfd46fd4008cbe7103181fc5cd64392a74426e96    MQTT.md
100644 blob abdb55935d833dd4f4b79475aa7d63ffcb0cc9cd    README.md
040000 tree f1f80f844bb80389826198a15ec0f224a53525f8    build
100644 blob 2aefa3130f4ff753b5c3e538db53b9b186f12540    index.html
100644 blob 967b8f243420a9a8a07b8f429f0a7ba874a834ad    package-lock.json
100644 blob 35d9fa46f569395b25a87daef4820de42d071831    package.json
040000 tree f6bdc675a8f9af805867b5a19c263e5bbfe4c26c    src
040000 tree 09e231414b91779326447a0c8d5b3421aa2308c2    static
040000 tree ad94369cfdd2038a552e44fc0abbd1738113b5e6    test
100644 blob 0b96f21c27a3759cecde02fba1e050d86a8e9a54    yarn.lock
复制代码
```

*   一个 blob 包含了哪些？

```
git cat-file -p [tree hash fragment]
就是一个具体的文件。
复制代码
```

*   detached HEAD 是什么？

```
分离头指针。
`git checkout [commit hash fragment]`，切换到分离头指针状态，不与任何branch或tag关联，git会认为这是不重要的，当成垃圾清理掉。
缺点：切换分支后，需要用`git branch [branch name] [commit hash fragment]`新建一个分支，否则会丢失原消息。
优点：可以基于某一次commit切出分支，然后新建一个commit，快速会退到想要的版本。
复制代码
```

*   HEAD 可以指向什么？

```
它位于.git/HEAD。
可以指向分支或者commit，**但其实分支归根结底还是指向了commit**。
git log 查看HEAD指针指向的分支名：`(HEAD->foo, bar, master)`
可以快速diff，`git diff HEAD [commit hash fragment]`。
父亲的父亲diff：`git diff HEAD HEAD~2`，`git diff HEAD HEAD^^`。
复制代码
```

*   如何修改最新一次 commit 的 message？

`git commit --amend` 注意：不能在团队的集成分支上，做这样的变更，仅适用于本地。

*   如何修改老旧 commit 的 message？

```
git rebase -i [父 commit hash fragment]
reward
添加修改后的commit message
注意：不能在团队的集成分支上，做这样的变更，仅适用于本地。
复制代码
```

*   git stash pop stash@{n} 还能做什么操作？

```
当前分支的本地代码未提交的情况下，pull了领先的远程分支代码，此时远程代码会覆盖本地代码。
git比较聪明，它不会完全将本地的代码扔掉，即使没有人为的生成一次commit记录，也会自动为我们在stash下生成一次记录，以免造成重大的代码丢失。
复制代码
```

*   gitflow 模式下，如何规范版本发布？

<table><thead><tr><th>版本号升级</th><th>gitflow 对应</th></tr></thead><tbody><tr><td>bug -&gt; patch</td><td>hotfix</td></tr><tr><td>feature-&gt;minor</td><td>release</td></tr><tr><td>系统重构 -&gt;major</td><td>release</td></tr></tbody></table>

但是在 scrum 的情况下，迭代非常快速，若所有 feature 都升级 minor，会导致 minor 数字很大，该怎么处理这种情况？

只升级 minor 时，在 commit 提交信息中，添加以下信息：

<table><thead><tr><th>类型</th><th>提交信息</th></tr></thead><tbody><tr><td>bug patch</td><td>[bug patch]</td></tr><tr><td>feature patch</td><td>[feature patch</td></tr></tbody></table>

*   创建一个新的项目并上传到 git

```
git init
git ac
git remote add origin remote repository URL
复制代码
```

*   git 参数 --decorate 是什么？
    
    *   有 short，full，auto，no 几种值，--decorate=short
    *   打印出 commit 的 ref name。
    *   short 时，ref name 前缀 refs/head,refs/tags / 和 refs/remotes 不会打印
    *   full 时，完整前缀会被打印
    *   auto 时，如果输出是一个终端，会按照 short 的方式打印；非终端会显示全部
    *   no 时，会隐藏 HEAD 和 tag 等等 refs 信息
    *   默认值是 short
*   cherry pick 是什么？
    

[如何理解 git cherry pick？](https://github.com/FrankKai/FrankKai.github.io/issues/172)

*   清空所有本地 git 缓存

```
git rm -r --cache .
复制代码
```

*   error Command "husky-run" not found

rm -rf .git/hooks/

*   一次完整的 rebase 流程

```
1. git checkout feature
2. git rebase master
3. resolve conflicts
4. git add .
5. git rebase --continue
复制代码
```

*   git revert 和 git reset 的区别

```
git revert 生成一个新的commit的方式回滚到上一个或者指定commit版本的代码，原理是Head继续前进。有存在被回滚掉的commit分支代码合并过来时，代码正常被合并

git reset 删除某个commit之后的代码，原理是Head向后退。有存在被回滚掉的commit分支代码合并过来时，被reset掉的代码仍然会合并上来
复制代码
```

### oh-my-zsh 常用命令

#### 缩写全写对照表

<table><thead><tr><th>缩写</th><th>全写</th></tr></thead><tbody><tr><td>gst</td><td>git status</td></tr><tr><td>gaa</td><td>git add .</td></tr><tr><td>gcmsg ""</td><td>git commit -m ""</td></tr><tr><td>gp</td><td>git push</td></tr><tr><td>glog</td><td>git log --oneline --decorate --graph</td></tr><tr><td>gl</td><td>git pull</td></tr><tr><td>gf</td><td>git fetch</td></tr><tr><td>gfa</td><td>git fetch --all --prune</td></tr></tbody></table>

#### 使用小技巧

*   如何修改默认指令的参数，比如，glog 的 decorate 默认是 short，我想指定 glog 的 decorate 为 no，要怎么做？

`glog --decorate=no`

> 期待和大家交流，共同进步：
> 
> *   微信公众号： 大大大前端 / excellent_developers
> *   Github 博客: [趁你还年轻 233 的个人博客](https://github.com/FrankKai/FrankKai.github.io)
> *   SegmentFault 专栏：[趁你还年轻，做个优秀的前端工程师](https://segmentfault.com/blog/chennihainianqing)

> 努力成为优秀前端工程师！