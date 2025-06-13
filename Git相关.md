# 基本流程

![image-20230411193927025](C:\Users\CARRYCHOU\AppData\Roaming\Typora\typora-user-images\image-20230411193927025.png)

# 基本命令

- 设置提交的用户信息，主要包括用户名和邮箱

  - git config --global user.name "runnob"
  - git config --global user,email test@runnoob.com

  **如果去掉--global参数则只对当前仓库有效**

- 提交： git commit

  - git commit -m [message]将暂存区内容全部添加到本地仓库中
  - 也可以使用git commit \[file1] \[file2] -m [m]提交暂存区的指定文件到仓库区
  - git commit -a参数设置修改文件后不需要使用git add添加暂存区，直接提交
  - git commit --amend可以修改当前提交记录表示的操作/说明；其实就是创建了一个新的提交覆盖了旧的提交，因此提交记录对应的Hash值也会改变
  - 【所有文件暂存并提交】git commit -am ‘message’ ，-am等同于-a -m
    -a参数可以将所有已跟踪文件中的==执行修改或删除操作的文件都提交到本地仓库==，即使它们没有经过git add添加到暂存区，这是-a的功能，除此之外没有对**指定文件**打包暂存和commit操作的。
    **注意:** 新加的文件（即没有被git系统管理的文件）是不能被提交到本地仓库的。

- 创建分支A：git branch A；

  - git branch 查看所有分支（分支其实也是文件夹路径）
  - git branch A 创建命名为A的分支
  - git checkout -b A 创建并切换到命名为A的分支；**可以看作同一个文件目录及内容的副本，在支线操作**
  - git branch -f main
  - git branch -r查看远程版本库分支列表
  - git branch -a查看所有分支列表，包括本地和远程
  - git branch -d dev 删除dev分支，如果在分支中有一些未merge的提交，那么会删除分支失败，此时也可以使用git branch -D dev强制删除dev分支
  - git branch -m oldName newName 给分支重命名
  - git branch -f <branchname> [start-point]强制将某分支名设置到当前提交记录
  - git branch -u <remote_branch> <new_branch>创建一个新分支名跟踪远程分支remote_branch;功能相同的有git checkout -b <new_branch> <remote_branch>
  - git branch -vv查看当前分支的上游分支
  - git branch --set-upstream-to=origin/<branch_name>

- git checkout可以操作文件也可以操作分支

  - git checkout filename放弃单个文件的修改
  - git checkout .放弃当前目录下的修改
  - git checkout master 将分支切换到master
  - git checkout -f branch-name 强制切分支

- 切换到分支A：git switch A或者git checkout A。**实际上是改变了HEAD的指向，A除了为分支名之外也可以为提交记录的哈希值**

- 将A合并到分支B：
  - git merge B，实际上就是在A的下面创建一个A和B共同的子节点C，包含了A和B的所有修改。**个人认为也可以理解为在A的下面重复B所作的所有修改**。==注意合并后的节点有两个父节点，第一个父节点一般是他的直接父节点，第二个父节点是基于共有节点下其他分支做的修改后的版本。==
  - git rebase A,变基，是当前分支基于的节点变为A最新的提交，即在A下创造当前分支的副本，**谨慎使用**。
  - git rebase A B        可以将B基于的节点变换为A最新的提交
  
- echo 有显示、回声的意思，在命令行中表示打印显示，比如echo This is a test后面会在控制台中显示This is a test；也可以将信息输入到指定文件中echo "This is a test." > ./test.txt。表示新建一个test.txt，里面有对应的内容。

- git add .表示添加当前工作目录文件到暂存区；后常跟git commit -m "message", 进行提交；最后使用git push推送服务器

- touch的命令是创建空文件，并修改时间戳

- git status：查看仓库状态 可以查询当前仓库是否有改动

- git log 查看本地仓库中所有的提交记录；**git show commitId**查看指定commit hashID的所有修改：**git show commitId fileName**查看指定commit中某个文件的修改

- git branch -vv可以查看详细的分支信息，上游分支会被显示在分支名称胖的放括号中。

# git理解

- Git 和其它版本控制系统（包括 Subversion 和近似工具）的主要差别在于 Git 对待数据的方法。 概念上来区分，其它大部分系统以文件变更列表的方式存储信息。 Git 不按照以上方式对待或保存数据。 反之，Git 更像是把数据看作是对小型文件系统的一组快照。当你每次重新提交更新代码时，或在 Git 中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git 对待数据更像是一个**快照流**。

- Git是一个分布式版本控制系统，它使用”快照”的概念来跟踪文件的变化。在Git中，每次提交（commit）都会创建一个快照。快照是文件或目录在某个特定时间点的状态的记录。每次提交都会记录当前工作目录中所有文件的快照。

- 快照是指将当前工作目录中的所有文件和目录的状态存储在一个特殊的数据结构中，称为Git仓库（repository）。每次提交操作会创建一个新的快照，并将其链接到前一个提交，形成一个提交历史。

- 每一次commit都是一次代码快照，

- Git分支不是复制所有数据，而是指向提交对象的指针。在执行“转移”(Git add )操作时，git会计算每个文件的有效性值，并将当前版本的文件快照保存在转移区域中并等待提交。

- 在 Git 中的绝大多数操作都只需要访问本地文件和资源

- **分离的 HEAD 就是让其指向了某个具体的提交记录而不是分支名**。在命令执行之前的状态如下所示：

  HEAD -> main -> C1

  HEAD 指向 main， main 指向 C1

# 在git提交树上移动

- 使用^向上移动一个提交记录；使用~<num>向上移动多个提交记录；比如git checkout A^

- 强制移动某分支指向的记录（**注意，并没有改变当前指向HEAD**）可以用git branch -f A B ,将分支A强制指向提交记录B，此时B的结果为分支A的当前版本。

- 使用HEAD分离分支与提交记录，让HEAD不指向分支名而指向提交记录可以在进行下一步提交时，原分支对应的版本不变，如下。其实就是使用git checkout &{hash}切换，但目标不是如main，bugfix之类的分支名而是哈希值

  <img src="C:\Users\CARRYCHOU\AppData\Roaming\Typora\typora-user-images\image-20230403210622797.png" alt="image-20230403210622797" style="zoom:50%;" />

- 从HEAD分离状态所作的一些更改如果需要保留可以使用switch的-c命令，比如git switch -c <new-branch-name> ；如果希望退出分离状态则使用git switch -

- 要撤销之前所作的更改有两种方法：

  - 第一种使用git reset，回退后面制定提交记录哈希值或者使用HEAD相对引用，比如git reset HEAD~1.**但要注意的是，这种方法只能在本地仓库中使用**。

  - 第二种是**git revert**，该方法可以在与其他人共享的远程仓库中使用，其原理是提交一个与需要撤销的更改相反的更改，生成一个新的提交记录，而该记录下，之前做的更改已经被撤销。**需要注意的是，由于git revert 本质是提交一个新的记录，因此在使用前务必保证HEAD指向的是需要做修改的分支或提交记录，更改才能被有效撤销**

    <img src="C:\Users\CARRYCHOU\AppData\Roaming\Typora\typora-user-images\image-20230403211750126.png" alt="image-20230403211750126" style="zoom:50%;" />

# git提交记录整理

- git cherry-pick [哈希值1] [哈希值2]可以直接将两个提交记录变化放到当前指向下。**非常优雅，如摘樱桃一般，但该命令需要知道提交记录的哈希值**

- git rebase -i HEAD~{NUM} 会调出一个UI指定排列向上NUM的提交顺序并创建调整后提交顺序后的副本

# git revert和git reset的区别

- ==撤销（revert）被设计为撤销公开的提交（比如已经push）的安全方式，`git reset`被设计为重设本地更改==

  因为两个命令的目的不同，它们的实现也不一样：重设完全地移除了一堆更改，而撤销保留了原来的更改，用一个新的提交来实现撤销

  两者主要区别如下：

  - git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit
  - git reset 是把HEAD向后移动了一下，而git revert是HEAD继续前进，只是新的commit的内容和要revert的内容正好相反，能够抵消要被revert的内容
  - 在回滚这一操作上看，效果差不多。但是在日后继续 merge 以前的老版本时有区别

  > git revert是用一次逆向的commit“中和”之前的提交，因此日后合并老的branch时，之前提交合并的代码仍然存在，导致不能够重新合并
  >
  > 但是git reset是之间把某些commit在某个branch上删除，因而和老的branch再次merge时，这些被回滚的commit应该还会被引入

  - 如果回退分支的代码以后还需要的情况则使用`git revert`， 如果分支是提错了没用的并且不想让别人发现这些错误代码，则使用`git reset`

- 总结 如果代码已经被推送到远端想要抹去记录
  git reset SHA[抹去提交代码之后的SHA] --hard [清除本地记录]
  git push origin 【分支名】 --force [推送到远端]
  git revert 与git reset的区别
  git revert 后多出一条commit ，提醒同事，这里有回撤操作，只能撤回最新的提交

- git reset 直接把之前 commit 删掉，非git reset --hard的操作是不会删掉修改代码，如果远程已经有之前代码，需要强推 git push origin 【分支名】 --force

- 在运行git reset head后，如果出现unstaged changes after reset。这是因为在master以外的新分支上，repos感知不到这个阶段的改变，你要用add和stash，让能让其知晓，从而做想要的回滚。

- 在git reset后，**之前新建的文件git不会删除而是会解除追踪，需要手动使用文件操作rm删除**。而修改的文件则会回复修改

# 提交的技巧

- git commit --amend
- git branch -f main HEAD
- git tag v1 C6 // 创建锚点
- git describe main // 查看main分支距离锚点的距离有几个提交记录 以及当前分支的哈希值

# 删除

- 删除本地分支

  git branch -d localBranchName

  **但有时当事分支包含了未合并的改动，或者当事分支是当前所在分支，无法用-d删除，此时如下：**

  **git branch -D branch-name强制删除本地分支**

- 删除远程分支（**注意动远程的仓库东西一定要push**）

  git push origin --delete remoteBranchName
  
  也可以如下有条理一些
  
  > git branch -d -r <branch-name> // 在本地删除远程分支
  >
  > git push origin <branch-name> //将删除推送远端

- 通过rm命令删除文件，和通过git rm删除的区别

  - 直接rm删除，仅仅是删除工作区中对应的文件和目录

  - git rm删除，不仅仅删除工作区中对应的文件或目录，还把删除操作添加到暂存区中。**rm删除后，可以通过git add命令或是git rm命令将删除添加到暂存区，git rm删除，帮我们屏蔽了删除过程中有关暂存区的操作，因为针对直接rm命令删除后，还需要手动添加到暂存区。**然后使用git commit提交命令将文件从版本库中删除

  - 场景：把工作区某个IDEA的配置文件添加到git版本库里面了，想仅仅是删除版本库里面的findme.xml，保留工作区里面的findme.xml。

    这个时候还是需要使用git rm 命令，只是需要加上--cached参数。

    - git rm --cached只会删除暂存区的文件或是目标，并不影响工作区。然后将暂存区的删除提交，则删除了版本库的文件而工作区保留。

# 将本地代码推动到远端

- 使用git push将本地版本的分支推送到远程服务器上的分支
  - 命令格式：git push <远程主机名>  <本地分支名>：<远程分支名>
  - **在clone到本地版本库时，所使用的远程主机名会自动被命名为origin，如果想使用其他主机名，需要使用git clone的-o参数指定**
  - 如果本地分支名与远程分支名相同，则可以省略冒号
  - 如果要合并到主干分支，可以在本地上直接合并merge了进行push

# 冲突解决与制造

- **制造冲突**
  - 初始化仓库及文件 git init git clone git branch
  - 在新分支上更改并提交文件
  - 在主分支上更改并提交文件
  - 执行合并，触发冲突
- 如何查看冲突
  - 在执行两个分支merge时如果存在冲突，会进入到合并的中间态，大概为**main|MERGING**的样子
  - 此时输入git status会输出具体有哪些文件冲突了
- 如何解决冲突
  - 此时针对那些文件使用vim一一打开编辑，从<<<<HEAD到====是当前HEAD指针文件的内容，从\=\=\=\=到>>>>new_branch是要合并分支的内容，分割线之外是两个都没有改动的内容，将这个文件增删改查至需要的内容
  - 使用git add /git commit提交

# Git Checkout远程分支

- git fetch origin //获取所有的远程分支

- git branch -a 列出所有可以checkout的分支

- 从远程分支拉取更改

  - **请注意，你不能直接在远程分支上进行更改，因此，你需要该分支的副本，如下：git checkout -b fix-failing-tests origin/fix-failing-tests，将执行如下：**

    - 创建了一个名为fix-failing-tests的新分支
    - checkout那个分支
    - 将更改从origin/fix-failing-tests拉到该分支

    现在有了那个远程分支的副本后，可以将提交推送到该远程分支

# 更新本地代码

- ![image-20230417154522523](C:\Users\CARRYCHOU\AppData\Roaming\Typora\typora-user-images\image-20230417154522523.png)**可以看到工作树除本地分支外，还有一个远程分支。远程分支反映了远程仓库在你最后一次与它通信时的状态，`git fetch` 就是你与远程仓库通信的方式了！**

  **需要注意的是，`git fetch` 并不会改变你本地仓库的状态。它不会更新你的 `main` 分支，也不会修改你磁盘上的文件。**

- 快速的更新main分支i并推送到远程git pull --rebase; git push

- 当远程分支中有新的提交时，你可以像合并本地分支那样来合并远程分支。也就是说就是你可以执行以下命令:

  - `git cherry-pick o/main`
  - `git rebase o/main`
  - `git merge o/main`
  - 等等

  实际上，由于先抓取更新再合并到本地分支这个流程很常用，因此 Git 提供了一个专门的命令来完成这两个操作。它就是我们要讲的 `git pull`。

  git pull 其实就是git fetch与git merge origin/main(或其他远程分支)的缩写！

- 更新分支代码，与本地指定分支自动合并

  - git pull origin remote_branch:local_branch更新远端代码到本地
  - git pull origin remote_branch

- git fetch的作用是，从远端服务器获取到某个分支的更新到本地仓库。**与git pull不同的是，git fetch在获取到更新后，并不会进行合并操作，这样能给用户预留一个操作空间**

  git fetch origin remote_branch:local_branch 获取远端更新（分支名不同）

  git fetch origin remote_branch 获取远端更新（分支名相同）

# 撤销操作

**如果commit过就用reset，未commit就用checkout**

- git reset commit_id用于撤销当前工作区中**某些git add/commit操作**，可以将工作区内容回退到历史提交节点（但是没回退代码）。
- git reset --hard commit_id 强制回退历史节点及工作区代码
- git checkout .用于**回退本地所有修改而未提交的文件内容**
- git checkout -file_name 仅回退某个文件的未提交改动
- git checkout commit_id将工作区代码回退到某个提交版本


# 随笔

- git如果新建文件但没有commit或者add，则git并没有对文件进行tracked，此时若想要删除需要使用windows/linux控制台文件命令rm，而不是git rm

- 对于新的文件，一般修改之后要git add之后再进行commit，但对于git已经形成track比较早的老文件，再git的新版本中，可以直接修改，直接commit

- git 切换分支时 报错 error: Your local changes to the following files would be overwritten by checkout，本地修改将会被checkout操作覆盖，出现这种情况的原因是**更改的文件没有提交暂存区和本地仓库就切换分支**。

  - 解决方法：可以依次执行如下命令

    git add 文件名 // 将修改文件提交至暂存区

    git commit -m "message for this commit"  // 将修改文件提交至个人仓库

- git restore命令是撤销，把文件从缓存区撤销，回到未被追踪的状态

  - 文件在暂存区且在暂存区后未作修改的情况

    **使用Git restore --staged \<file>**把文件从暂存区移动到工作区，即文件不再被追踪

  - 文件在暂存区且在暂存区后又修改过的情况

    - 使用git restore --staged \<file>把文件从暂存区移动到工作区，且不会撤销修改的内容

    - 使用git restore <file>文件仍在暂存区且会撤销文件修改的内容

  - 文件在本地代码库已经修改的情况

    - 使用git add \<file>把文件重新放到暂存区，且保留文件的修改
    - 使用git restore \<file>文件仍在本地代码库且会撤销文件的修改

  - **总的来说，对于git restore \<file>命令，会撤销文件的修改，是文件恢复到暂存区或本地代码库（取决于文件在修改前的状态）**

  - **对于git restore --staged \<file>命令，把文件从暂存区撤回到工作区，保留文件最后一次修改的内容**

- 在错误的分支写代码
  - git stash save "本次存储的说明或者名称" 将新增的代码存起来
  - git checkout 切换到正确的分支
  - git stash pop 将存储的代码推出栈顶的那一个 //也可以输入参数推出某一次存储的代码

- 当不小心使用git reset --hard 删除了代码
  - git reflog命令查看所有的提交记录，包括reset也会有一个记录
  - 使用git reset --soft命令回到误删的操作之前可以恢复的代码

- 远程有一个空仓库，本地有一个项目，将本地项目推上远程仓库
  - git init 初始化项目为git项目
  - git remote add origin [URL]远程仓库地址 连接到远程仓库
  - git add .将代码添加到缓存区
  - ...

- 在大型团队工作是，很可能main被锁定了，需要一些OPull Request流程来合并修改，如果直接commit到本地main，然后试图push修改，将会收到报错**![远程服务器拒绝]main -> main(不允许推送这个分支，你必须使用pull request更新这个分支)**

- 使用git clone下来的一般默认只有一个分支（一般是==master==）,如果要拉其他分支，可以==直接使用git checkout <branch-name>会自动从远端拉一个下来==；

  更有逻辑性的也可以用==git checkout -b <branch_name> origin/<branch_name>==

- 推送代码到指定分支，之后再合并

  ```git
  git push <远程主机名> <本地分支名>:<远程分支名>
  ```

  如果本地分支名与远程分支名一致，则可省略冒号后的部分

  ~~~c
  git push <远程主机名> <本地分支名>
  ~~~

- 如果代码已经被推送送到远端，想要抹去记录，如下：

  1. 首先将本地的内容回退到自己想要的版本

     1. git log/reflog 查看自己想要版本的哈希值

     2. git reset --hard 版本号 // 回退到想要的版本

        或者 git reset --hard HEAD~n // 回退到HEAD指向前几个目录

     3. 如果用--soft，则git status会发现代码仍在缓存区中，可以git rm [--cached] 文件名。 // ==cached只删除索引，不删除文件，与add相对应，不加--cached连同索引文件一起删除==

  2. 重新推送至远端覆盖

     ==注意直接git commit然后git push origin <分支名>会报错，提示当前分支版本低于远程分支版本==

     用git push origin \<分支名\> --force即可！

- **==git进行push或pull时看的是commit的hash值，通过此来判断版本新旧==**，再进行pull时，远端版本需要新于本地版本；push时本地版本需要新于远端版本。==否则，如果本地分支追踪远程，则直接合并；如果没有追踪，会报non-fast-forward==。
  
- git push --set-upstream origin dev_bim	//PUSH并设置上游
  
- git merge后对主分支进行git log会有所有合并分支的提交记录，其排序按**==提交时间==**进行排序。==如果合并分支没有被删除，对应的提交记录上会括号表明分支，如果已删除，则对应的提交记录上没有括号表明分支，但也会有，并也是括号表明==

- 在B分支上，git rebase A，表示将B分支的基变换到A上。变基后，B分支上与A存在冲突的修改会被要求解决冲突，而后给与新的message。而被作为基底的目标分支A不会变化，只是为了保证B的线性提交

- git恢复已经删除的分支：

  如果我们知道删除分支时的散列值，就可以将某个删除的分支恢复过来

  ~~~C++
  git branch <branch_name> <hash_val>
  ~~~

  如果不知道想要恢复的分支的散列值，可以用`reflog`命令找出来

  ![image-20230427094418387](C:\Users\CARRYCHOU\AppData\Roaming\Typora\typora-user-images\image-20230427094418387.png)

  （==可以找checkout切换到的分支的散列值==）

  ~~~C++
  reflog命令：
      显示整个本地仓库的commit,包含所有branch的commit，甚至包括已经撤销的commit。
      比如上面可以直接 git branch <branch_name> HEAD@{1}
  ~~~

- 在使用git checkout/switch切分支之前，一定要记得把修改提交或者复原，不然工作区切过去修改会到另一个分支、

- git对于已经添加到版本库的文件，删除时，使用

  git rm -f <文件名>

  直接删除文件，连同删除索引文件与删除工作区本地文件

  git rm -cache <文件名>

  在索引区删除文件且不追踪（==注意，工作区有未追踪的文件是切不了分支的==）

- 创建本地仓库后，==本地仓库会有一个本地分支和远程分支，远程分支反映了远程仓库（在你上次和他通信时）的状态==

  git fetch 将更新git remote中所有远程repo所包含分支的最新commit-id，将其记录到FETCH_HEAD文件中

  **FETCH_HEAD：** 是一个版本链接，记录在本地的一个文件中，指向着目前已经从远程仓库取下来的分支的末端版本。

- git reset 表示实质是将当前HEAD指针移向某个提交记录，根据目标提交记录与当前提交记录的修改集合的保存位置，有--soft, --mix, --hard三个参数：
  - --soft表示将回滚期间的代码修改存放至Index暂存区
  - --mix表示将回滚期间的代码修改存放至工作区，未提交至暂存区的状态
  - --hard表示将回滚期间的代码修改完全删除

# cherry-pick摘取

- 在有两个分支，一个稳定版本分支A，和一个开发版本B的情况下，希望讲B中的某个功能提到A上，这时候不能直接用merge合并，因为可能会导致稳定版本混乱，此时就要用到cherry-pick将B中的某些提交提交到A上。
- 首先需要切换到A中（要提交的分支上），接下来使用git cherry-pick xxxx（提交哈希）。如果用sourceTree可视化管理工具，可以直接找到想要的提交进行遴选。

# ssh公钥用法

- 首先配置好git后，进入git的bash命令行，执行`ssh-keygen -t rsa –C lisi@xxx.com *# 或者默认操作* ssh-keygen -t rsa`会在本机上生成一串ssh代码，这串代码是本机在git网络上识别证；在需要接入的gitlab仓库上的自己账号上的editProfile里的sshKey上，将该id_rsa.pub文件上的代码贴上去，即赋予了本机在该gitlab仓库上通行的权限。
- 如果使用了TortoiseGit等工具，需要在工具上使用puTTYgen也生成一个ssh识别码，贴到需要接入的gitlab的仓库上，然后在工具上的Pagenant上Add该ssh识别文件。
- **如果配置好公钥，并添加到gitlab自己账号下的profile中，长时间不用后发现无效化了又要输密码，这时候找出自己本机下c盘用户目录下的.ssh文件夹的.pub文件，用vscode打开，在添加到profile一次就好，报错已存在也无所谓，会重新识别到。**

# 团队合作，pr和mr

- pull request和merge request是一个东西
- 前者是请求别人拉你的代码，后者是请求合并进主干

# git子模块

- submodule解决团队维护难题，每个团队负责的模块就是一个git仓库，所有仓库被包含在一个主仓库下。
- 在Git仓库A中，通过`git submodule add ...(仓库B的地址，即git clone时后面那串东西)`，可以把仓库B当作仓库A的submodule，此时A就成了主项目。【注：B也可以做A的主项目，通过在仓库B执行`git submodule add ...(A地址)`即可，因为二者都是完整Git仓库，在建立父子关系前，没有差异的。】执行操作后，会在当前父项目下新建个文件夹，名字就是 submodule 仓库的名字。这个文件夹里面的内容，**是 submodule 对应 Git 仓库的完整代码。**
- 注意父子关系仅被父项目识别，子项目本身并不知道自身是否是子项目，在父项目的`.gitmodules` 文件中，主项目还保存了对应 submodule 的版本号（commit id），**没有冗余存储 submodule 的代码**。当你在文件夹B中做commit后，文件夹B里面就有了新的 commit id。此时主项目A中所记录的 submodule 的commit id也会更新。所以，你`cd ..`回到文件夹A后，会发现A有变更了，变更内容是：旧commid id变成了新的commit id。
- 如何在主项目仓库，拉取 submodule 的更新
  - cd submodule 后 `git pull`
  - 主项目执行`git submodule update --remote [submodule文件夹相对路径]`
  - 主项目执行 `git submodule update [submodule文件夹相对路径]`

- clone 包含 submodule 的仓库
  - 先`git clone 主项目仓库`并进入主项目文件夹，这时候submodule的文件夹都是空的。
  - 执行`git submodule init [submodule的文件夹的相对路径]`。
  - 执行`git submodule update [submodule的文件夹的相对路径]`。
  - 二三步可以合并，git submodule update --init [submodule的文件夹的相对路径]。

# gitlab

- gitlab仓库只有owner可以为其他人创建账号，如果没有账号，但是希望拉取代码，可以由gitlab的成员制作一个token标记，外部接口可以通过该标记拿到仓库中该成员权限的代码，属于成员权限的自由行为
- gitlab可以在push的封闭的门上加一个有钥匙的活动锁，即ssh-key。本机在生成一个git的key之后，将该key放到gitlab账号成员的sshkey栏中，本机就可以通过git直接读取修改gitlab的数据了。

# git报错

- git pull报错error: cannot lock ref ‘refs/remotes/origin/xxx‘:ref，这种情况是在pull代码时，本地远端不能追踪仓库，这一般是由于仓库已经将该分支删掉，但是本地的orgin却还存在，此时会报错，可以使用git remote prune origin进行修改。

# git使用场景

- 有的已经推向远端的代码需要回退，可以选择该commit，进行git revert xx（该commit），会创建一个相反的提交，回退掉该提交的代码。
- 在git reset后强制推送时，一般选择git push ---force-with-lease，不要选择-force，前者更加安全，会识别远端是否存在本地中没有的提交。

