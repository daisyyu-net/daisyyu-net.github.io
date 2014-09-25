# All thing you need to know about git ~~名字取的特别大言不惭~~

## 1. Configuration

本地git配置 是所有git教程的第一步。这货很重要是因为他可以减少你的很多工作啊。一般的设置就是`user.name` 和 `user.email` 这两项。

**Example**

> git config --global user.name "daisy"   
git config --global user.email "daisyyu1990@gmail.com"

你要是一上来没有设置这个的话呢，假如说 现在你要从远程git clone下来一个private的repository,因为他是private的 所以可能只有特定的一个user list里面的人才能读取或者修改。所以你在git clone的时候他就会问你你的用户名是多少啊 密码是多少啊。每次都要输入是不是绝对特别麻烦！！！！

其实你可以把它想成是`.bashrc` 他里面就是去存一些你之后会用到的环境变量的。他的默认存储路径是`.gitconfig`你也可以自己编辑这个配置文件。是不是感觉特别像`bashrc`!! 他还有一个比较像`.bashrc`的地方是 `git config`是可以用来定义alias的。

**Example**

> git config --global alias.co checkout

这样之后你就可以用`git co`来代替`git checkout`啦。少打了好多字母有没有！！

奥。对了 那个`--global`的意思是你的这个设置对所有的在你当前机器上的人用的git账号是试用的。就是全局设置文件。不加`--global`的话 是只对你当前用户有效。就是Daisy。

**只做了解**

git config --global core.editor 偶尔会用到 是用来改变默认的文本编辑器的。默认是`vim`。你在用`git commit`的时候 他会让你写log 自动打开的就是`vim`对吧。就是用来改那个的。对你没啥用。但是如果以后要去多环境系统里面作业的话，很有可能别人把全局变量设置成了例如`Emacs`之类的 咱不会用。就把本地的配置文件改回`vim`就好啦。

> git config --global core.editor "vim"

## 2.Basic Operation

这一章主要是一些基本的`git`操作命令。包括（clone,add,rm,commit,reset,revert,checkout）首先 介绍一下`git`存储文件的状态。

`git`一共有三个存储状态:

- _History_： 就是一直所说的本地仓库
- _Stage_：中文一般叫做缓冲区/缓存区。所有你没有`commit`掉的修改的/添加的内容都属于这个状态。
- _working dictionary_：你刚刚`checkout branch`/`commit`之后的状态。也就是原始的 完全没修改过的 也没有提交过的状态。
    
好。看图！！

![Fig. 1](http://marklodato.github.io/visual-git-guide/basic-usage.svg)

这个状态转换图画的挺明白的吧。下面开始先说说怎么创建一个`git`。

### 2.1 create/download project

一般来说，所谓的创建`git`分成两种情况
- Project 已经有了。但是在远程。你需要做的就是拷贝过来 然后继续修改。这个是最常见的。
- Project 还没有。是全新的 你需要自己去创建的。

##### project已经有了

这个就很简单啦。

> git clone <url>

之前有的project就被clone到本地啦。包括所有之前修改的记录 所有的分支。

##### project要新建

新建一个`git`需要做一些初始化的工作

> git init # 这个就是创建一个 .git的目录 
  
> git add . # 把当前目录加进去 ( working dictionay -> stage )

> git commit # 提交修改到本地仓库（stage -> history)
  
**注:**
`.git`目录用于存放你之后修改的乱七八糟的记录之类的隐藏目录。(你可以`ls -a` 来看一下我有没有骗你= =)`.git`这个目录要是被删除掉了的话 你本地所有的修改啊什么的历史记录就都没了。

### 2.2 add VS rm

这俩是一对快乐的好基友。 

`git add .`用于把当前目录添加到stage。`git add -a`用于把所有文件都添加到stage.

`git rm <filename>` 用于把指定的文件从当前的stage中删除掉。

### 2.3 commit, revert, reset

这三个东西要一起讲。`revert`和`reset`都是用来取消`commit`的。


#### commit 

关于`commit`，就是把stage的东西提交到history的一个操作。三种常用的

> git commit 

输入这个之后会蹦出来一个vim的界面 让你去填写这次commit都修改了些啥东西。这个很重要的，大概就是相当于对当前这个版本的一个描述。你之后用来追溯版本的时候非常好用。

> git commit -m "You can add some comment here directly, without vim`

这个就是比较懒的人用`-m`参数把要写的东西直接写好 就不用调用vim了。

> git commit -a

这个事更懒的人用的。。这个相当于`git add .` 然后 `git commit`.

> git commit -a -m "comment"

最懒的人用的版本来了。。。

#### revert

`commit`的逆向操作。他是把从现在 到你之前要revert到的的那个版本中间所有的修改，逆向的做一次。然后`commit`。

**Example**

> git revert HEAD~3 #向之前退回去3次commit（就是恢复到4个commit之前的状态）

#### reset

`commit`的逆向操作。他是删除掉前面的commit的操作，然后回退到之前的状态。`reset`用的比较多 其实一般不用`revert`的。关于两者的区别总结，后面有。别着急。

**Example**

> git reset HEAD^ # 向前退回去一个版本

> git reset -hard origin/master # 退到远程的状态，远程概念之后讲。

> git reset <commit_code_number> # 退回到特定的number所对应的commit,关于这个号码 参见后面的git log

#### revert VS reset

1. `git revert`是用一次新的`commi`t来回滚之前的`commit`，`git reset`是直接删除指定的`commit`。
2. 在回滚这一操作上看，效果差不多。但是在日后继续merge以前的老版本时有区别。因为`git revert`是用一次逆向的`commit` Naturize之前的提交，因此日后合并老的branch时，导致这部分改变不会再次出现，但是`git reset`是之间把某些`commit`在某个branch上删除，因而和老的branch再次merge时，这些被回滚的`commit`应该还会被引入。
3. `git reset` 是把`HEAD`向后移动了一下，而`git revert`是`HEAD`继续前进，只是新的`commit`的内容和要`revert`的内容正好相反，能够抵消要被`revert`的内容。（注意，这里的前指的是未来，后指的是过去）


### 2.4 孤独的checkout

`checkout`一共有两种作用

> git checkout HEAD <fileName> # 撤销指定未提交(commit)文件的修改

> git checkout <branchName> # 切换到指定分支。关于分支 第四章讲。

其实还有一个比较常用

> git checkout -b <branchNaem> # 创建制定本地分支并且checkout

### 2.5 本章总结

要添加本地文件到stage,用`add`。删除用`rm`。要提交修改到history（本地仓库)用`commit`，后悔了用`reset`或者`revert`。要把现在正在修改的文件还原到上一次commit之后的初始状态 用`checkout`，要把正在修改的文件还原到之前commit再之前的状态，用`reset`.


## 3. log

第二章的内容挺多的。辛苦啦。消化消化。第三章咱们聊点儿轻松的。关于查看日志。总共有仨命令`log`, `show` 和 `status`

### 3.1 git log

这个可以列出所有的commit, 你可以认为它是在查询history的状态。注意啦！这里有个比较有用的东西。你运行`git log`之后 会看到若干个如下信息。

> commit <commit code number>

> Author: daisy < daisyyu1990@gmail.com >

> Date: XXX

>     comment

comment就是你当时`git commit`的时候自己写的那个comment。重要的东西是<commit code number> 记得之前在`reset`的时候提到过commit的哈希码，就是这个啦。

### 3.2 git show

常用两个：

> git show # 返回当前commit与上一次commit的diff（其实就是这次都commit了啥)

> git show <commit code number> # 返回当前commit与制定的commit之间的差距

### 3.3 git status

可以认为它是比较 `work dictionary`和`stage`之间差距的命令。

> git status # 返回未提交改动

好啦 看了三章了。该休息一下啦。起来活动活动 揉揉眼睛~ 劳逸结合才能效率更高。剩下的内容不多啦。

## 4. branch 

branch这东西特别有用啊。一般来说 一个大的project 你如果要做其中的一部分。你就可以新建一个自己的branch，去做改动 改动好了再merge到主分支。（回忆一下 wenjian 让咱们warmup hyracks的时候 就是一人建立一个分支对不对？）

### 4.1 查看分支

> git branch -r # 查看远程分支 关于什么叫远程，看下章

> git branch -v # 查看本地分支

这个没啥好讲的 就是返回个名字 为了让你看看你现在在哪个分支啊 你之后要co或者merge哪个分支。

> git branch --merged # 查看已经被合并到当前分支的分支

> git branch --no-merged #查看没被合并到当前分支的分支

这俩就是用来看看当前分支是从哪几个分支一起merge来的 不常用。只做了解。

### 4.2 切换分支

> git branch <branchName> # 切换本地分支

> git checkout -b <branchName> # 创建本地分支并切换 之前讲过这个。

> git checkout -b <newBranch> <branch> #基于branch分支创立新的branch并切换（常用)

### 4.3 创建/删除分支

> git branch <newBranh> #新建

> git branch -d <branchName> #删除指定分支

> git branch -D <branchName> # 强制删除某个分支 (未被合并的分支被删除的时候需要强制) 

### 4.4 merge

这一步的作用主要是 之前咱自己建立好自己的分支了。然后我这个分支的东西都做完了。我就要把它合并到主分支。对吧。

所以要做的就是

> git commit -a -m "commit all the change of current branch"

> git checkout master # 切换到主分支

> git merge Daisy_dev_branch_V0.1 # 与你的分支合并

### 4.5 rebase

`rebase`可以理解为另外一种方式的`merge` 先看图。

![fig. 2](http://git-scm.com/figures/18333fig0328-tn.png)

之前说过，一般的整合分支的方法是 `merge`，它会把两个分支最新的快照（C3 和 C4）以及二者最新的共同祖先（C2）进行三方合并，合并的结果是产生一个新的提交对象（C5）。其实我们可以通过另外一种方法实现。这个就是`rebase`。他的意思是我可以先记录一下 我（C3）相对于(C2)的改变，然后在（C2）的基础上把它变成（C4） 这时候认为（C4）是（C3）的base 所以这个过程叫做rebase. 然后在（C4）的基础上去用之前记录的改变去把（C4）变成（C5）。

这个。。。。有点。。。不太好讲明白= =。意思就是re-base嘛 换了一个基准的节点。但是因为我把现在相对于原来base的节点的改动都记录下来了，而且现在的心得base其实是在原来base基础上的另外的改动，所以我是可以用现在的base直接来做我现在的改动的。。。感觉还是没说明白T T

### 4.6 cherry pick

其实`cherry pick`的作用就是我把其他分支的某些commit借用到我的分支。举个栗子。

假设我们有个稳定版本的分支，叫v2.0，另外还有个开发版本的分支Daisy-v3.0，我们不能直接把两个分支合并，这样会导致稳定版本混乱，但是又想增加一个Daisy-v3.0中的功能到Daisy-v2.0中，这里就可以使用cherry-pick了。这个东西就是从字面上理解啊。可以把每个commit都假设程一个樱桃。cherry-pick就是我就拿特定的一个或者几个樱桃 加到我的现在的分支里面。

## 5 remote branch

最后一章啦。在坚持一下。

到现在为止，我们的所有操作都是在本地。有了版本控制之后 确实daisy自己在处理版本回滚啊 修改啊包括保留不同特性这一方面都变得特别方便了。但是，daisy是要和亚麻的其他~~猥琐男~~工程师们合作的，所以要有一份共同的版本仓库。这样每人处理一个或者几个分支 最后把代码都贡献到远程master才能拼凑出来一个完整的项目产品。

这个大家一起维护的换成的版本仓库就是remote.也就是我们在`git clone`的时候 我们download下来的就是remote的仓库。

### 5.1 查看远程版本库

> git remote -v # 查看远程的版本库

> git remote show <remote> # 查看远程特定版本库（见 注 ）


**注** 远程的版本库有的时候不只是有一个啊，就像昨天我说的，一个project会有很多个版本库的。例如正在开发的叫dev，准备发布的叫staging,发布的叫rel（release),mainline的叫truck。这个分组而定啦。不同的组命名规范不一样。这些是我们组的远程版本库命名。

### 5.2 add/get

> git remote add <remote> <url> # 你基本不会用到。知道有就可以了。

> git fetch <remote> # 获取远程一个特定的版本库。

你可以把`git clone`理解为获取远程所有版本库，把`git fetch`想成只获取其中的某个。

### 5.3 upload/download

> git pull # update当前的本地分支，让当前本地分支与现在远程版本库里面的内容同步

> git push -u origin master #将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库,这个也比较常用)

一般来说，你本地有个master分支，然后你自己checkout了一个daisy_dev分支。那么你要这么做:

> git checkout -b daisy_dev # create branch

> balabala # dosomething for your own branch

> git commit -a -m "change log"

> git checkout master # 切换回本地分支 准备合并

> git pull # 把本地master分支up-to-date

> git merge daisy_dev # 合并版本

> git push # upload local repo to remote

这是一个基本的流程。要记住啊~~~

### 6. Cheet Sheet

胖子骗你说5是最后一章。特别坏！！！

![fig. 3](https://gitcafe.com/GitCafe/Help/raw/master/assets/images/Git_Cheat_Sheet.png)

而且胖子特别无耻。。cheet sheet都是上网抄的~~~ 完全没诚意！！

差不多感觉。。。内容就这么多了 当然还有什么`tag`啊`stash`什么的 感觉都不太重要（而且我也不太会用) 如果你需要的话 跟我讲。我再去看看 反正我也打算要了解一下。嗯。

不说了 睡觉啦 好困。

晚安 >.<

                                        -- By Sean.
