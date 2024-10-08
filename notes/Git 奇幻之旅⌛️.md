> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.v2ex.com](https://www.v2ex.com/t/948186)

第一天: 本地仓库
---------

故事的主角是小明，一个刚入门编程的小白。他正在为一个项目写代码，但是他发现每次修改代码都很麻烦，因为他要不断地备份文件，而且很容易弄混版本。有一天，他听说了一个叫 Git 的神奇工具，可以帮助他管理代码的变化。他决定尝试一下，于是他打开了终端，输入了下面的命令：

```
git init # 初始化一个本地仓库
git add . # 添加所有文件到暂存区
git commit -m "first commit" # 提交第一次修改到本地仓库
```

这样，他就成功地创建了一个 Git 仓库，并且保存了他的第一个版本。他觉得很开心，因为这样他就不用担心代码丢失或者混乱了。😁

第二天: 远程仓库
---------

小明觉得自己的代码写得很不错，想要分享给其他人看看。但是他发现把文件发给别人很麻烦，而且如果别人也修改了代码，就很难合并。有一天，他听说了一个叫 GitHub 的网站，可以免费托管 Git 仓库，并且方便和其他人协作。他决定尝试一下，于是他注册了一个 GitHub 账号，并且在网站上创建了一个空的仓库。

然后，他在终端输入了下面的命令：

```
git remote add origin https://github.com/xiaoming/myproject.git # 添加远程仓库地址
git push -u origin master # 推送本地 master 分支到远程仓库
```

这样，他就成功地把自己的代码上传到了 GitHub 上，并且和远程仓库建立了联系。他觉得很兴奋，因为这样他就可以和全世界的程序员交流了。😍

第三天: 分支管理
---------

小明在 GitHub 上发现了一个很有趣的开源项目，想要参与其中。但是他不想直接修改别人的代码，而是想先在自己的电脑上做一些改进，然后再提交给项目的作者。有一天，他听说了一个叫分支的概念，可以让他在不影响主线的情况下，创建自己的代码版本。他决定尝试一下，于是他在终端输入了下面的命令：

```
git clone https://github.com/someone/awesome-project.git # 从远程仓库克隆项目到本地
cd awesome-project # 进入项目目录
git checkout -b dev # 创建并切换到 dev 分支
```

这样，他就成功地在本地创建了一个 dev 分支，并且和远程仓库的 master 分支分开了。他觉得很自由，因为这样他就可以随心所欲地修改代码了。😎

第四天: 合并与冲突
----------

小明在 dev 分支上修改了一些代码，觉得很满意，想要把自己的改进合并到 master 分支上，然后推送到远程仓库，让项目的作者看看。有一天，他听说了一个叫合并的操作，可以把两个分支的代码合并成一个。他决定尝试一下，于是他在终端输入了下面的命令：

```
git checkout master # 切换到 master 分支
git merge dev # 合并 dev 分支到 master 分支
git push origin master # 推送 master 分支到远程仓库
```

这样，他就成功地把自己的代码合并到了 master 分支，并且推送到了远程仓库。他觉得很骄傲，因为这样他就可以为开源项目做出贡献了。😊

但是，有时候合并分支并不是一帆风顺的。有一次，小明在 dev 分支上修改了一个文件，而项目的作者也在 master 分支上修改了同一个文件，并且先于小明推送到了远程仓库。当小明想要合并分支时，就发生了冲突。有一天，他听说了一个叫解决冲突的方法，可以手动选择保留哪些代码。他决定尝试一下，于是他在终端输入了下面的命令：

```
git pull origin master # 拉取远程仓库的 master 分支
git merge master # 合并 master 分支到 dev 分支
# 打开冲突文件，编辑保存
git add . # 添加所有文件到暂存区
git commit -m "fix conflict" # 提交修改到本地仓库
git push origin dev # 推送 dev 分支到远程仓库
```

这样，他就成功地解决了冲突，并且把自己的代码推送到了远程仓库。他觉得很成就感，因为这样他就可以和其他人协作了。😄

第五天: 标签管理与忽略文件
--------------

小明在 dev 分支上开发了一个新功能，觉得很完美，想要给这个版本打一个标签，方便以后查找。有一天，他听说了一个叫标签的概念，可以给某个版本起一个有意义的名字。他决定尝试一下，于是他在终端输入了下面的命令：

```
git tag v1.0 # 给当前版本打一个 v1.0 的标签
git push origin v1.0 # 推送标签到远程仓库
```

这样，他就成功地给自己的代码打了一个标签，并且推送到了远程仓库。他觉得很方便，因为这样他就可以快速定位到某个版本了。😎

但是，有时候有些文件是不需要被 Git 管理的，比如编译生成的临时文件，或者敏感信息的配置文件。有一天，他听说了一个叫忽略特殊文件的方法，可以让 Git 自动忽略掉这些文件。他决定尝试一下，于是他在项目根目录下创建了一个. gitignore 文件，并且写入了下面的内容：

```
*.tmp # 忽略所有.tmp 后缀的文件
config.ini # 忽略 config.ini 文件
```

这样，他就成功地让 Git 忽略掉了这些特殊文件，并且不会被提交到仓库中。他觉得很安全，因为这样他就可以避免泄露隐私或者浪费空间了。😊

第六天: 大小写敏感
----------

小明和小红是一个团队的成员，他们都在 GitHub 上为同一个开源项目贡献代码。有一天，小明在本地修改了一个文件的名字，把它从 [README.md](http://README.md) 改成了 [Readme.md](http://Readme.md) ，然后提交并推送到了远程仓库。小红在自己的电脑上拉取了最新的代码，但是她发现自己的文件名还是 [README.md](http://README.md) ，而且 Git 提示她有一个未合并的文件。她很困惑，不知道为什么会出现这样的情况。

原来，这是因为 Git 在不同的操作系统上对文件名大小写的敏感度不同。在 Linux 和 Mac OS X 上，Git 是区分大小写的，所以 [README.md](http://README.md) 和 [Readme.md](http://Readme.md) 是两个不同的文件。但是在 Windows 上，Git 是不区分大小写的，所以 [README.md](http://README.md) 和 [Readme.md](http://Readme.md) 是同一个文件。当小明把文件名改成了 [Readme.md](http://Readme.md) 时，Git 认为他删除了 [README.md](http://README.md) ，并且创建了一个新的文件 [Readme.md](http://Readme.md) 。当小红拉取代码时，Git 认为她需要合并这两个文件，所以出现了冲突。

有一天，他们听说了一个叫解决大小写不一致导致的合并冲突的方法，可以让 Git 在 Windows 上也区分大小写。他们决定尝试一下，于是他们在终端输入了下面的命令：

```
git config core.ignorecase false # 设置 Git 在 Windows 上也区分大小写
git mv README.md Readme.md # 重命名文件
git commit -m "rename file" # 提交修改
git push origin master # 推送到远程仓库
```

这样，他们就成功地解决了大小写不一致导致的合并冲突，并且保持了文件名的一致性。他们觉得很开心，因为这样他们就可以避免以后出现同样的问题了。😁

第七天: 撤销错误提交与恢复误删文件
------------------

小明和小红在开发一个新功能时，不小心提交了一些错误的代码，导致项目无法运行。他们想要撤销这些提交，但是又不想丢失他们的修改。有一天，他们听说了一个叫 reset 的命令，可以让他们回退到某个版本，但是保留他们的修改。他们决定尝试一下，于是他们在终端输入了下面的命令：

```
git reset HEAD~2 # 回退到两个版本之前，保留修改
git status # 查看修改的状态
git add . # 重新添加修改到暂存区
git commit -m "fix bug" # 重新提交修改
git push -f origin master # 强制推送到远程仓库
```

这样，他们就成功地撤销了错误的提交，并且重新提交了正确的代码。他们觉得很轻松，因为这样他们就可以修复 bug 了。😊

但是，有时候 reset 命令也会带来麻烦。有一次，小明在回退版本时，不小心加了一个–hard 选项，导致他的修改全部丢失了。他很慌张，不知道如何找回他的修改。有一天，他听说了一个叫 reflog 的命令，可以让他查看所有的提交历史，包括已经被删除或者回退的提交。他决定尝试一下，于是他在终端输入了下面的命令：

```
git reflog # 查看所有的提交历史
git reset --hard c761f5c # 回退到指定的版本
git status # 查看修改的状态
```

这样，他就成功地找回了他丢失的修改，并且恢复到了正确的版本。他觉得很幸运，因为这样他就可以继续开发了。😄

第八天: 多人协作与冲突处理
--------------

小明和小红在同一个分支上开发一个新功能，他们经常需要拉取对方的代码，然后合并到自己的代码中。有一天，他们听说了一个叫 pull 的命令，可以让他们一步完成拉取和合并的操作。他们决定尝试一下，于是他们在终端输入了下面的命令：

```
git pull origin master # 拉取并合并远程仓库的 master 分支
```

这样，他们就成功地把对方的代码合并到了自己的代码中，并且保持了同步。他们觉得很方便，因为这样他们就可以避免手动合并的麻烦了。😎

但是，有时候 pull 命令也会带来问题。有一次，小明和小红在同一个文件上修改了同一行代码，导致出现了冲突。他们很困惑，不知道如何解决这个冲突。有一天，他们听说了一个叫解决冲突的方法，可以让他们手动选择保留哪些代码。他们决定尝试一下，于是他们在终端输入了下面的命令：

```
git pull origin master # 拉取并合并远程仓库的 master 分支
# 打开冲突文件，编辑保存
git add . # 添加所有文件到暂存区
git commit -m "merge conflict" # 提交修改到本地仓库
git push origin master # 推送到远程仓库
```

这样，他们就成功地解决了冲突，并且把自己的代码推送到了远程仓库。他们觉得很成就感，因为这样他们就可以和对方协作了。😄

第九天: rebase 和 merge 的区别
-----------------------

小明和小红在同一个项目上开发不同的功能，他们分别在自己的分支上提交了一些代码。有一天，他们想要把自己的代码合并到主分支上，但是他们不知道应该用 rebase 还是 merge 。有一天，他们听说了一个叫 rebase 和 merge 的区别的概念，可以让他们选择合适的方式来合并代码。他们决定尝试一下，于是他们在终端输入了下面的命令：

```
# 小明在 dev1 分支上
git checkout dev1 # 切换到 dev1 分支
git rebase master # 把 dev1 分支变基到 master 分支
git push -f origin dev1 # 强制推送 dev1 分支到远程仓库
git checkout master # 切换到 master 分支
git merge dev1 # 合并 dev1 分支到 master 分支
git push origin master # 推送 master 分支到远程仓库

# 小红在 dev2 分支上
git checkout dev2 # 切换到 dev2 分支
git merge master # 合并 master 分支到 dev2 分支
git push origin dev2 # 推送 dev2 分支到远程仓库
git checkout master # 切换到 master 分支
git merge dev2 # 合并 dev2 分支到 master 分支
git push origin master # 推送 master 分支到远程仓库
```

这样，他们就成功地把自己的代码合并到了主分支上，但是他们发现了一个不同的地方。小明用了 rebase 命令，他的提交历史是一条直线，没有任何分叉；小红用了 merge 命令，她的提交历史是有多个分叉和汇合的结构。他们觉得很好奇，不知道这两种方式有什么优缺点。

原来，rebase 和 merge 的区别是：

*   rebase 是把自己的分支变基到目标分支上，也就是把自己的提交历史放在目标分支的最后，这样可以保持提交历史的整洁和线性。
*   merge 是把目标分支合并到自己的分支上，也就是把目标分支的提交历史和自己的提交历史合并成一个新的提交，这样可以保持提交历史的完整和真实。

rebase 和 merge 各有优缺点：

*   rebase 的优点是可以让提交历史看起来很简洁，方便查看和管理；缺点是会改变提交历史，可能导致冲突或者丢失信息。
*   merge 的优点是可以保留提交历史的原貌，方便追溯和恢复；缺点是会让提交历史看起来很复杂，不容易理解和维护。

所以，在选择 rebase 还是 merge 时，要根据具体的情况和需求来决定。一般来说：

*   如果你想要保持一个干净和线性的提交历史，你可以用 rebase ；
*   如果你想要保留一个完整和真实的提交历史，你可以用 merge ；
*   如果你想要在公共的分支上合作，你应该用 merge ，避免用 rebase ，因为 rebase 会改变提交历史，可能导致其他人的困扰；
*   如果你想要在私有的分支上开发，你可以用 rebase ，因为 rebase 可以让你的提交历史更清晰，方便你自己管理。

第十天: 撤销错误合并和恢复误删的分支
-------------------

小明和小红在合并分支时，不小心合并了错误的分支，导致项目出现了很多 bug 。他们想要撤销这次合并，但是又不想丢失他们的修改。有一天，他们听说了一个叫 revert 的命令，可以让他们用一次新的提交来回滚之前的提交。他们决定尝试一下，于是他们在终端输入了下面的命令：

```
git log # 查看提交历史
git revert <commit ID> # 回滚指定的提交
git push origin master # 推送到远程仓库
```

这样，他们就成功地撤销了错误的合并，并且用一次新的提交来记录这次回滚。他们觉得很安全，因为这样他们就不会丢失任何修改了。😊

但是，有时候 revert 命令也会带来麻烦。有一次，小明在回滚一个合并时，不小心加了一个–no-commit 选项，导致他的修改没有被提交，而是被放在了暂存区。他很慌张，不知道如何恢复这次回滚。有一天，他听说了一个叫 reset 的命令，可以让他回退到某个版本，并且保留或者丢弃他的修改。他决定尝试一下，于是他在终端输入了下面的命令：

```
git reset --soft HEAD^ # 回退到上一个版本，并且保留修改
git status # 查看修改的状态
git add . # 重新添加修改到暂存区
git commit -m "fix bug" # 重新提交修改
git push -f origin master # 强制推送到远程仓库
```

这样，他就成功地恢复了这次回滚，并且重新提交了正确的代码。他觉得很轻松，因为这样他就可以修复 bug 了。😊

第十一天: 删除和恢复分支
-------------

小明和小红在完成一个功能后，想要删除自己的分支，因为他们觉得这个分支已经没有用了。有一天，他们听说了一个叫 delete 的命令，可以让他们删除本地或者远程的分支。他们决定尝试一下，于是他们在终端输入了下面的命令：

```
git branch -d dev1 # 删除本地的 dev1 分支
git push origin --delete dev1 # 删除远程的 dev1 分支
```

这样，他们就成功地删除了自己的分支，并且释放了一些空间。他们觉得很爽快，因为这样他们就可以开始新的功能了。😎

但是，有时候 delete 命令也会带来后悔。有一次，小明在删除一个分支后，发现自己还需要这个分支上的一些代码。他很懊恼，不知道如何找回这个分支。有一天，他听说了一个叫 reflog 的命令，可以让他查看所有的提交历史，包括已经被删除或者回退的提交。他决定尝试一下，于是他在终端输入了下面的命令：

```
git reflog # 查看所有的提交历史
git checkout -b dev1 <commit ID> # 用指定的提交创建一个新的 dev1 分支
git push origin dev1 # 推送 dev1 分支到远程仓库
```

这样，他就成功地找回了自己的分支，并且恢复到了正确的版本。他觉得很幸运，因为这样他就可以继续使用这个分支了。😄

最后
--

到此为止，我已经给你讲完了小明和小红的故事，你觉得怎么样？👏

原文地址:

[https://www.wangwangit.com/Git%E7%A5%9E%E5%A5%87%E4%B9%8B%E6%97%85/](https://www.wangwangit.com/Git%E7%A5%9E%E5%A5%87%E4%B9%8B%E6%97%85/)