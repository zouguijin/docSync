title: Git Study

---

# Git Study
### Git Introduction

Git是一种开源的分布式版本管理系统。

每台安装了Git的机器上都会维持一个Git的本地仓库。

### 本地版本库的创建与管理

#### 创建与文件提交

- 初始化一个Git仓库：`git init`；
- 添加文件到Git仓库（[背后的原理](#gitadd-principle)）:
  1. `git add <file>` ，可以使用多次，添加多个文件；
  2. `git commit -m "提交说明文字"` ，完成一次提交。

#### 查看仓库状态

- `git status` ，查看本地仓库的[工作区](#workplace)状态，命令会告诉我们，当前有哪些文件被修改了，现在的状态是”没有添加任何文件“OR”已添加待提交“，此外还会对后续操作提示相应的命令；
- `git diff` ， 如果有文件被修改了，可以用该命令查看具体的修改内容（有"-"号的一行说明有删减，有“+”的一行说明有增加）。

#### 版本回退与回退的撤销

- `git log` ，查看提交的历史记录，以便确定回退到哪一个版本，精简输出可以使用`git log --pretty=oneline`；<span id="versionback"></span>

- `git reflog` ，查看命令的历史记录，可以用于确定版本回退之前的最新版本号`commit_id` ；

- `git reset --hard <commit_id>` ，`commit_id` 是版本号，是SHA1计算得出的十六进制数字，每提交一次就会新生成一个`commit_id` ，版本号没有必要每次都输入完整，保证前几位不同就可以。此外，`HEAD` 表示当前版本号，`HEAD^` 表示上一个版本号，以此类推，`HEAD~100` 表示上100个版本号。

  **注意** ：对于只有一个版本的版本库是无法再回退到上一个状态（即最初初始化的状态）

#### 工作区与暂存区

- <span id="workplace">工作区（Working Directory）</span>

  也就是当时使用`git init` 初始化的本地仓库的目录/文件夹。

- 版本库（Repository）

  工作区内的隐藏目录`.git` ，这个目录不算工作区，而是属于版本库。版本库中最重要的部分是暂存区（stage/index）这也是Git与SVN不同之处，同时还有Git自动创建的第一个[分支](#branch)`master` 和指向`master` 的[指针`HEAD`](#pointer-HEAD) 。

- 工作区与暂存区的关系

  这里通过`git add <file>` 的<span id="gitadd-principle">工作原理</span>来解释一下工作区与暂存区之间的关系。

  1. `git add` 添加的时候，实际上是将文件或者文件的修改添加到暂存区中；

  2. `git commit` 提交文件或文件修改，实际上是将暂存区的所有内容提交到当前分支中。

     **注意**：

     - 提交之后，工作区就没有任何新的修改，暂存区也没有任何内容，同时产生了新的版本。

     - 提交的时候，Git只负责将已经加入暂存区的修改内容提交到分支中，即若`git add` 之后再对文件进行修改，那么此时修改的内容是不会提交到分支中的。

       `git diff HEAD -- <file>` ，可以用于查看工作区与版本库中最新版本之间的区别。

#### 管理与修改、删除

Git跟踪并管理的是**文件的修改部分** ,而非整个文件。

所以，如果修改没有从工作区添加到暂存区，那么这些修改就无法被提交到版本库的最新版本中。

- `git checkout -- <file>` ，直接放弃对工作区的所有修改，这比一行一行的恢复更省力气，有时候对于不知道修改了哪部分内容的情况也很有用。

- `git rm --cached <file>` ，将添加到暂存区的修改撤销（unstage），并将修改重新放回工作区，如果此时仍想撤销所有的修改，则执行命令`git checkout -- <file>` ；

- 如果已经向本地版本库提交了不合适的修改并想要撤销时，需要[版本回退](#versionback)操作，前提是还没有推送到远程仓库。

- 若要删除一个文件，需要在工作区和版本库中同时删除该文件 `git rm <file>` ，并在删除完成后重新提交`git commit -m <file> ` 一次：

- 若在工作区错删了文件，则可以借助版本库，将该文件恢复到最新一次提交的状态：`git checkout -- <file>` *（当然，如果回收站还没清空的话，也可以去回收站看看）*；

- **注** ：`git checkout` 的原理是，利用版本库中的最新版本替换工作区的版本。


### 远程仓库

可以自己搭建Git服务器作为远程仓库，也可以使用Github的免费仓库（但是是公开的所有人可见的）。

一台电脑上也可以建立多个版本库，只要不在同一目录中即可。

Git支持SSH协议。

#### 添加远程库与推送Push

在完成Github注册和SSH Key添加之后，就可以在Github上创建一个新的Git仓库，然后根据相应的提示，将本地仓库的文件推送（Push）到远程仓库中。

- `git remote add origin <仓库地址>` ，在本地仓库目录下执行，将本地仓库与远程仓库进行关联，同时为远程仓库定义了新的名字`origin` ，当然也可以使用别的名字；
- `git push -u origin master` ，将本地仓库的内容推送到远成仓库，实际上是将当前分支`master` 的内容推送到远程仓库的`master` 分支，由于刚开始远程仓库是空的，所以第一次推送的时候，需要参数`-u` ，这样在推送的同时，还会建立起本地分支`master` 和远程仓库中`master` 分支的联系，以后推送和拉取内容就可以简化命令，即`git push origin master` 和

#### 从远程库克隆Clone

- `git clone https://github.com/zouguijin/docSync.git` 

- `git clone git@github.com:zouguijin/docSync.git` 

  Git支持多种协议，默认的git（所使用的是ssh协议），此外还可以使用https协议（但是https协议有时候比较慢，而且每次推送都需要输入口令，使用ssh就不需要输入口令，`get clone` 的时候都要口令...）。

### 分支（Branch）

创建属于自己的分支，其他人是看不到的，一个未完成的项目可以在分支上不断更新，直到完成之后再一次提交到`master` 主分支上。

#### 创建与合并

- 原理：

  一个分支就是一条时间线，随着不断地提交逐渐变长。最初只有主分支即`master` 分支，同时有一个`master` 指针指向主分支的最新提交，之前说的指针`HEAD` 指向的是指针`master` ，而不是指向最新提交。即：

  > `HEAD` -> `master` -> `最新提交` 

  每次提交之后，`master` 分支都会向前延长，同时指针`master` 都会指向最新的提交，指针`HEAD` 的指向不变。

  创建新的分支，例如`branch` 时，即创建新的指针`branch` ，指向与`master` 相同的提交，此时若将指针`HEAD` 指向`branch` ，则表示切换当前分支，由主分支切换到`branch` 分支。

  > `master` ->`最新提交` <- `branch` <- `HEAD`  

  如果在`branch` 分支下提交新的修改，那么`branch` 分支就会向前延长，指针`branch` 指向最新提交，但此时指针`master` 保持切换前的指向位置不变。

  合并分支，也就是将主分支的`master` 指针指向`branch` 指针所指向的最新提交即可，合并分支后，一般都会将工作时使用的新分支`branch` 删除，即删除`branch` 指针。

  **注** ：可见，所谓创建分支，只是创建了一个指针，切换、合并分支，只是改变了指针的指向，删除分支，只是将对应的指针删除，所有的操作都不涉及内容的改变。

- 命令操作

  - `git branch` ，查看分支情况，当前分支前会用`*` 标记；
  - `git branch <name>` ，创建分支；
  - `git checkout <name>` ，切换分支，由当前分支切换到指定命名分支；
  - `git checkout -b <name>` ，创建&切换分支；
  - `git merge <name>` ，合并分支，将指定命名分支合并到当前分支上；（默认使用`Fast Forward` 模式，该模式下删除分支后，分支信息也随之删除，即看不到历史合并信息；若希望能看到历史合并信息，就需要禁用FF模式，并最好添加注释信息，即`git merge --no-ff -m "注释信息" <name>`）
  - `git branch -d <name>` ，删除指定命名分支。

- 合并冲突<span id="conflict-solve"></span>

  如果两个需要合并的分支各自都有新的提交，而且两者的提交在同一位置有不同的表述，在合并的时候就会出现冲突，即不能简单地删除、添加或者替换。

  冲突出现的时候，在显示分支的括号中会多出`|MERGING` 字样，表示此时必须解决合并冲突，分支切换操作被禁止，这时需要利用`git diff` 查看文件内容，并手动修改冲突的内容，然后重新添加和提交即可。（Git会用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容）

  `git log --graph` 可以看到分支的合并情况，或者简化版`git log --graph --pretty=oneline --abbrev-commit` 。

- 分支管理策略

  - 保证主分支`master` 是稳定的，仅仅用于发布公开的、可以使用的新版本；
  - 如果团队合作的话，需要新建一个团队开发提交的分支，例如`dev` 分支，平时的提交与合并都在该分支上完成，一个完整的版本完成之后再将`dev` 分支合并到主分支上。

#### Bug分支

当遇到Bug的时候，可以切换到需要修复Bug的分支上，在该分支上创建临时的Bug分支，在临时分支上修复Bug，然后合并原分支上，最后删除临时分支即可。

此时，如果手头上的工作没有做完（工作区有正在修改的内容，如果将Bug修复完成之后就添加、提交，那么之前正在处理但未处理完成的工作就会和修复的Bug一起添加并提交上去，这是我们不想看见的），那么这时候就需要将未完成的工作**先拿出工作区并暂存**起来：

> `git stash` 

此时，利用`git status` 查看工作区，会看见工作区是干净的。

完成Bug修复之后，可以再将之前的工作取出来放进工作区继续完成：

- `git stash list` ，若有多个工作，可以先查看列表，再决定需要恢复哪个工作；
- `git stash apply` ，恢复工作之后，暂存的内容并不会自动删除，需要使用`git stash drop` 将相应的内容删除；
- `git stash pop` ，推荐使用，恢复工作的同时，可以将暂存的内容也删除；
- 若要指定恢复或者删除哪一条`stash` ，可以添加后续命令`stash@{x}` ，`x` 可以从`stash` 列表中选取。

#### Feature分支

为项目开发一个新的功能时，最好在项目分支上新建一个分支，功能完成之后再将新功能的代码合并到项目分支中。

如果在合并之前，需要放弃该功能分支，则需要通过强行删除的操作完成：

> `git branch -D <name>`

#### 推送与抓取

- `git remote -v` ，查看远程库的信息，会给出本地可以抓取和推送的远程库的地址以及远程库的命名（默认是`origin`）；

- `git push origin <name> ` ，推送分支，即将指定分支上的所有本地内容推送到远程仓库中的**相对应**的分支上，可以推送本地`master` 分支内容，也可以推送其他分支的内容；

- `git clone <git/https>` ，其他用户从远程库克隆的时候，默认情况下只能看到`master` 分支（当然其他分支也随之克隆下来了，只是不稍微调整是看不到的），前面说了，一般不会直接向`master` 分支推送修改，所以需要在本地创建远程仓库`origin` 的分支`branch` （这里的`branch` 需要与远程仓库的`branch` 同名）：

  > `git checkout -b branch origin/branch` 

  只有这样，才能建立本地与远成仓库的联系，之后的抓取`git pull` 和推送`git push origin branch ` 才可以进行。

- `git pull` ，抓取分支，首先需要建立本地分支与远程仓库相应分支之间的链接：

  > `git branch --set-upstream branch origin/branch` 

  在之前建立本地分支与远程库分支的基础上，可以将最新的提交从远程库相应的分支上抓取下来：

  > `git pull` 

  如果`git pull` 的时候出现冲突，则需要[解决冲突](#conflict-solve) 。

- 本地分支，若不推送到远程，只有本地可见。

### 标签（Tag）

版本发布的时候，为了今后方便查找，一般会使用标签的方式，采用有意义的文字标记当前版本，即可以将标签当作版本库的一个快照，本质上标签就是一个指向某一次`commit` 的指针，类似于分支指针（但是分支指针可以移动，标签指针不可以移动）。

- `git tag` ，查看所有标签；
- `git tag <name>` ，为当前分支生成一个标签，标签默认标记在最新的提交上；
- `git tag <name> <commit_id>` ，若想为历史的某一次提交生成标签，则找到该提交的`commit_id` 即可；
- `git tag -a <tagname> -m "注释信息" <commit_id>` ，创建标签并生成注释；
- `git show <tagname>` ，查看相应的标签信息；

**注** ：标签是按照字母顺序排序的，而不是按照时间排序。

- `git tag -d <tagname>` ，删除本地标签；

- `git push origin <tagname>` ，将标签推送到远程仓库中；

- `git push origin --tags` ，一次性地将本地标签全部推送到远程库中；

- 若想要删除的标签已经被推送到了远程库中，那么需要两步才能删除标签：

  >`git tag -d <tagname>` #首先，本地删除
  >
  >`git push origin :refs/tags/<tagname>` #然后，远程删除库中的标签

### Github

如果要参与开源项目，首先将开源项目`Fork` 到自己的仓库中，然后**从自己的仓库中`Clone` **，只有这样才能在本地修改之后，再次推送到Github上，若希望开源项目的官方接受自己的修改，则需要在Github上发起`Pull Request` 。

### .gitignore

.gitignore文件中的文件名将会在提交的时候被忽略，.gitignore文件需要放在版本库中。

.gitignore文件不需要从头开始编写，可以参照[官方文档](https://github.com/github/gitignore '.gitignore-doc')并进行相应的组合即可。

- `git add -f <file>` ，有时候.gitignore文件中禁止提交的文件类型中，有你希望提交的一份文件，则可以通过上述命令强行添加并提交；
- `git check-ignore -v <file>` ，当发现一个文件无法添加和提交时，需要检查.gitignore文件中哪一条规则写错了，可以使用上述命令进行检查。

### Git配置

- 当前仓库的配置文档存放在`.git/config` 文件中；
- 当前用户的配置文档存放在用户主目录下的隐藏文件`.gitignore` 中；
- `git config --global alias. <short-command> <origin-command>` ，配置别名，简化使用。

### Git服务器搭建

服务器系统采用Linux，推荐Ubuntu或者Debian，以下操作需要sudo权限。

1. 安装Git:

   `sudo apt-get install git` 

2. 创建Git用户，用于运行Git服务：

   `sudo adduser git` 

3.  添加公钥，保证用户的登录：

   收集所有需要登录服务器的用户的公钥，即`id_rsa.pub` 文件的内容，将公钥添加进服务器的`/home/git/.ssh/authorized_keys` 文件中，一行一个。

4. 初始化Git仓库：

   选定一个目录作为Git仓库，例如`/git/git-server.git` ，在目录`/git` 下执行命令：

   `sudo git init --bare git-server.git` 

   创建的是裸仓库，即没有工作区，服务器的目的是为了共享，而不允许用户登录到服务器上去修改，然后将Git仓库的所有者更改为之前添加的用户git：

   `sudo chown -R git:git git-server.git` 

5. 禁用shell登录：

   即禁止之前创建的用户git登录shell，可以通过编辑`/etc/passwd` 文件完成，将以下一行

   `git:x:1001:1001:,,,:/home/git:/bin/bash` 

   改为

   `git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell` 