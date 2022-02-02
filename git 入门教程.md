## Git 基本信息

### Git 特性

- Git 和其它版本控制系统的主要差别在于对待数据的方法：

  - 其他系统：

     存储每个文件与初始版本的差异，如图：

    [![img](https://ailanxier.top/img/blog/git-note-images/delta.png)](https://ailanxier.top/img/blog/git-note-images/delta.png)

  - Git：

     存储项目随时间改变的**快照**，把数据看作是对小型文件系统的一组快照。每次 commit 或保存项目状态时，Git 对当时的全部文件制作一个快照并保存这个快照的索引。为了高效，如果文件没有修改，Git **不再重新存储**该文件，而是只保留一个链接指向之前存储的文件。Git 对待数据更像是一个**快照流**：

    [![img](https://ailanxier.top/img/blog/git-note-images/snapshots.png)](https://ailanxier.top/img/blog/git-note-images/snapshots.png)

- Git 保护文件完整性：

  - Git 中所有数据在存储前都计算**校验和**，不能在不改变校验和的情况下修改文件。
  - 计算校验和的机制叫做 **SHA-1 散列**（hash，哈希），由 40 个十六进制字符组成。
  - Git 以校验和来引用文件，而不是文件名。

- Git 一般只增加数据，难以清除数据库中数据，使得 Git 操作一般都是**可逆的**

### 「三棵树」 🌳

Git 项目的三个工作区域（文件的集合）：

- **Git 仓库**，.git directory，保存项目的元数据和对象数据库的地方。
- **暂存区域**，或 Index，或 Staging Area，保存了**下次将提交的文件列表信息**，一般在 Git 仓库目录中。有时候也被称作「索引」，不过一般还是叫暂存区域。
- **工作目录**，Working Directory(Tree)，对项目的**某个版本**独立提取出来的内容，放在磁盘上可以使用或修改

### Git 基础操作

- `git config`：

  - 用于设置控制 Git 外观和行为的配置变量，它们存储在三个不同的位置：

    - `/etc/gitconfig` 文件:

      使用 `--system` 选项读写该文件，包含系统上每一个用户及他们仓库的通用配置。

    - `~/.gitconfig` 或 `~/.config/git/config` 文件：

      使用 `--global` 选项读写该文件，只针对当前用户，一般 Windows 设置此处。

    - 当前使用仓库的 Git 目录中的 config 文件（就是 `.git/config`）：

      针对该仓库。

      每一个级别覆盖上一级别的配置。

  - 用户信息：

    ```
    $ git config --global user.name "用户名"
      $ git config --global user.email 邮箱
    Copy
    ```

  - Git 操作别名 `alias`：

    - 相当于 `define`，不用输入复杂的操作命令，只需要输入它的别名

    - 以 `git log` 的显示优化为例子，可以以 Git 图形象展示分支和提交情况，并涂上颜色 🖍

      ```
      git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
      Copy
      ```

    - 对这一操作起别名 `showlog`，截取 `git` 之后的部分：

      ```
      git config --global alias.showlog "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
      Copy
      ```

  - 设置编辑器：

     默认编辑器为 Vim，入门不太友好，如果配置有 VSCode 或其他编辑器，可以修改默认编辑器：

    ```
    $ git config --global core.editor "code --wait"
    Copy
    ```

  - `git config --list` 命令来列出所有 Git 配置

- 获取**帮助文档**的三种方式：

  ```
  $ git help <verb>
  $ git <verb> --help
  $ man git-<verb>
  Copy
  ```

- 获取 Git 仓库

  - `git init` 创建 `.git` 的子目录，含有初始化的 Git 仓库中所有的必须文件，此时三棵树只有工作目录，其他两棵树都为空，后续可以添加远程仓库。
  - `git clone` 克隆现有的仓库。

- **忽略文件**：

  - 对于不希望 Git 加入版本控制的文件，可以通过 `.gitignore` 文件，列出要忽略的文件模式，一行一个规则（所有空行或者以 `＃` 开头的行都会被 Git 忽略）。

  - 经典格式：

    - 按文件名忽略单个文件：

      ```
      # 忽略当前文件夹下的 a 文件：
      a
      # 忽略所有子目录下的 a 文件
      */a
      Copy
      ```

    - 按后缀忽略文件：

      ```
      # 忽略所有的 .a 文件
      *.a
      # 用 ! 否定忽略 test.a 文件, 即使前面忽略了所有的 .a 文件
      !test.a
      # 忽略 /b/test.a,不能忽略 /b/b/test.a
      b/*.a
      # 忽略 b 目录及其子目录下所有的 .a 文件
      b/**/*.a
      Copy
      ```

    - 忽略文件夹：

      ```
      # 忽略根目录下的 a 文件夹，以下等价
      a/
      /a/
      /a/*
      # 忽略 a 文件夹, 不管是根目录下的 /a/, 还是子目录下的 /child/a/
      a/*
      Copy
      ```

    - 否定的格式和忽略格式一样，只需要加 `!`

    - glob 模式规则（简化版正则表达式规则）：

      - 以星号 `*` 通配任意个字符

      - 以问号 `?` 通配单个字符

      - 以方括号 `[]` 包含单个字符的匹配列表：

        ```
        # 忽略 debug0.log、debugA.log 等debug[a-zA-Z0-9].log       # 仅忽略 debug0.log、debug1.logdebug[01].log       	 # 不忽略 debug0.log、debug1.logdebug[!01].log     		  Copy
        ```

  - 要注意的细节：

    - `gitignore` 是从上到下一行一行匹配，**后面的会覆盖前面**的。

    - 默认编码是 GBK，所以**不能识别中文**。

    - 对于已经加入版本控制的文件，不能仅通过修改 `.gitignore` 来取消跟踪，还要使用：

      ```
      $ git update-index --assume-unchanged 文件路径Copy
      ```

    - 一般对于各种语言和项目有 `.gitignore` 模板

## Git 本地操作

### 文件状态

- **工作目录**下的每一个文件处于三种状态之一：

  - **已跟踪**，指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于**未修改 Unmodified，已修改 Modified 或已放入暂存区状态 Staged**。

    > 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

  - **已忽略**，在提交前已经被 `.gitignore` 规则忽略。

  - **未忽略且未跟踪**，不是以上两种状态的文件

  - 状态转换：

    [![img](https://ailanxier.top/img/blog/git-note-images/status.png)](https://ailanxier.top/img/blog/git-note-images/status.png)

- `git status` 查看文件状态：

  - 会显示了**当前所在分支**，以及这个分支同远程服务器上对应的分支是否偏离

  - 输出格式：

    ```
    Changes to be committed:# 已添加到暂存区的状态，包含 modified, new file, renamed, deletedChanges not staged for commit:# 工作目录的状态，未添加到暂存区，包含 modified, deleted	Untracked files:# 未忽略且未跟踪的新文件Copy
    ```

  - 使用 `-s` 选项让输出更简洁：

    - 例子输出：

      ```
      $ git status -sM  READMEMM RakefileA  lib/git.rb?? LICENSE.txtCopy
      ```

    - 状态分为**两列**，**左列**为上述所谓的**暂存区状态**，**右列**为**工作目录状态**。

    - `M` 表示文件被修改过，**左边**的 `M` 表示该文件被修改了**并放入了暂存区**，**右边**的 `M` 表示该文件被修改了但是还**没放入暂存区**。同时具有两列状态的还有 `D`，表示一个**已跟踪文件被删除**。

      > 上面 `Rakefile` 文件可能经历了**两次修改**，第一次修改后，加入到了暂存区中，有了左边的 `M`，之后再进行第二次修改，未加入到暂存区，此时就会得到右边的 `M`。若再将该文件放入暂存区，则只会输出左边的 `M`。

    - `A` 表示文件新添加到暂存区中，之前可能处于为未跟踪状态，只会出现在**左边**。同样只会出现在左边的还有**更名**操作产生的状态 `R`。

    - `??` 表示未忽略且未跟踪的新文件。

  - 当 `git status` 输出 `working tree clean` 时，或 `-s` 选项无输出时，表示当前没有修改，三棵树保持一致。许多命令都要求在 `working tree clean`，以防丢失修改。

### Git 个人开发操作

- 查看文件差异：

  - 直接上图：

    [![img](https://ailanxier.top/img/blog/git-note-images/diff.png)](https://ailanxier.top/img/blog/git-note-images/diff.png)

  - `git diff` 本身只显示**尚未暂存的改动**，而不是自上次提交以来所做的所有改动。所以暂存了所有更新过的文件后，运行 git diff 会什么也不输出。

- `git add` 添加进暂存区：

  - 在新版本的 Git 中，`git add .` 和 `git add -A` 操作效果一致，都是将未跟踪、已跟踪被修改，已跟踪被删除的文件添加到暂存区。
  - `git add -u` **不会将未跟踪**文件添加到暂存区，`u` 即 `update`，顾名思义就是只有**已被跟踪**的文件被更新（删除或被修改）才会添加到暂存区。

- `git rm` 删除：

  - 清除不小心添加到暂存区的文件，不想让 Git 去跟踪。如果已经提交了，需要用之前说的方式删除：

    ```
    $ git update-index --assume-unchanged 文件路径Copy
    ```

  - 不带选项时，是**同时删除**暂存区和工作目录下的文件，并且该文件的状态变为**左边**的 `D`，即删除这一动作也会被提交。

  - `--cache` 选项，**只删除暂存区**的文件，不删除工作目录的文件，比如一些中间过程文件。文件状态会同时出现在暂存区和工作目录：

    - 在暂存区中显示**左边**的 `D`

    - 在工作目录中显示**未跟踪**的 `??`

    - 当使用简洁输出状态的时，会产生以下奇妙输出：

      ```
      $ git rm --cache d$ git status -sD  d?? dCopy
      ```

  - 可以使用 glob 模式规则选择删除文件

- `git mv` **移动或更名**文件：

  - 格式：`git mv a b`，`b` 可以是另一个文件名，也可以是一个具体的文件路径。
  - 工作目录和暂存区的文件都会被移动
  - Git 会意识到这是一次改名，文件的状态会变成 `renamed` 或**左边**的 `R`。

- `git commit` 提交更新：

  - `-a` 选项，跳过使用暂存区，直接把已修改或者已删除的文件添加提交，但是对于**未跟踪的新文件**不会添加，一般只用在修改少量文件的提交。
  - `--amend` 选项，尽量在未推送到远程时使用（除非只有自己用），可以**替换**掉当前 `HEAD` 指向的提交（校验和改变），用于补充一些修改，保持 Git 记录工整。如果不使用 `-m` 编写新的提交信息，则会使用旧的提交信息。

### 后悔药：撤销操作 💊

- **强烈建议使用**的 `git restore`：

  - 新版本的撤销命令，分担了原来 `git checkout` 和 `git reset` 的大量任务，通常格式为：

    ```
    $ git restore [-s <tree-ish>] [--staged] [--worktree] 文件路径Copy
    ```

  - `[]` 表示这一选项或参数可有可无。不带任何选项时，则会默认从**暂存区**还原工作目录下的文件。

  - `-s <tree>` 选项，从 `<tree-ish>` 对应的提交还原文件，可以是 `HEAD`，分支名，或是提交的校验和前几位。

  - `--staged` 选项，从 **`HEAD`** 或者在设置 `-s <tree-ish>` 后，从 `<tree-ish>` 还原文件，只会改变**暂存区**。

  - `--worktree` 选项，一般和 `--staged` 一起用（因为没有 `--staged` 时就是会还原工作目录的），表示**同时改变暂存区和工作目录**。

  - 通过灵活使用上述选项，改变文件状态，基本上满足工作需求

- `git reset` 重置：

  - 当重置目标是文件时，`reset` 改变的是**暂存区**的对应文件，格式为：

    ```
    $ git reset [<tree-ish>] [--] 文件路径Copy
    ```

    加 `--` 会比较好一点，因为如果不幸有个分支名与路径名相同，那么 `reset` 是不能判断的（报错 `ambiguous argument`），所以要用 `--` 指定。

    > 重置文件后，会发现文件的状态变成了 `MM`，即**暂存区**的文件与 `HEAD` 和工作目录的文件都不同。这命令其实等价于：
    >
    > ```
    > $ git restore [-s <tree-ish>] --staged 文件路径Copy
    > ```

  - 其余情况，会移动 **`HEAD` 指向的分支** 到某一提交。格式为：

    ```
    $ git reset [<mode>] [<commit>]Copy
    ```

    此时有三种模式 mode：

    - `--soft`：只是将 `HEAD` 及其指向的分支移动到 `commit`，**不会改变**暂存区和工作目录。
    - `--mixed`：不指定 mode 时的**默认**模式，将会改变暂存区与 `commit` 一致，其余同 `--soft`，不改变工作目录。
    - `--hard`：较为**危险**的选项，将工作目录的**修改全部丢弃**，重置到 `commit` 一致，其余同 `--mixed`。
    - 以上三种模式影响范围递增，形象对比如下：

    ```
    原状态working  index   HEAD   target            A       B      C       D    $ git reset mode target     mode	    working    index    HEAD   --soft         A         B        D--mixed        A         D        D--hard         D         D        DCopy
    ```

    > 可以使用 `--soft` 模式实现所谓「**压缩提交**」的效果：
    >
    > 假设当前提交历史为 `a → b → c`，当不想保留 `b` 提交时，可以在 `c` 时使用 `--soft` 的 `reset`，使 `HEAD` 和其分支指向 `a`，同时因为暂存区不变，再提交一次即可，最终历史为：
    >
    > ```
    > a -> b -> c└--> d (HEAD)Copy
    > ```

- 万能的 `git checkout`：

  - 如果要移动 `HEAD`，格式为：

    ```
    # 切换到指定分支$ git checkout [-q] [-f] [-m] [<branch>]# 切换到 start_point，并在其上建立新分支$ git checkout [[-b|-B] <new_branch>] [<start_point>]Copy
    ```

    - 不同于 `reset`，`checkout` **仅会**移动 `HEAD`，不会移动分支。这使得 `HEAD` 可能进入 `detach` **分离**状态，即 `HEAD` 指向了某个**具体的提交记录**而不是分支名，可以通过新建分支解决。
    - `-B` 是强制建立分支，即使同名分支已存在，这会使得原同名分支移动到 `start_point`。
    - 切换分支时尽量保持 `working tree clean`，即三棵树保持一致，否则可能会丢失修改。

  - 如果要撤销文件修改，格式为：

    ```
    $ git checkout [<tree-ish>] [--] 文件路径Copy
    ```

    这会同时修改**暂存区和工作目录**的对应文件，使得与 `tree-ish` 一致，这会使得修改丢失，属于**危险操作**。

### Git 标签 🔖

- 分支很容易被人为移动，并且当有新的提交时，它也会移动，大部分分支还只是**临时**的。所以需要一个永远指向某个**提交记录**的标识，比如软件发布新的大版本，或者是修正一些重要的 Bug 或是增加了某些新特性。这就需要打**标签**。

- 标签不会随着新的提交而移动，它就像是提交树上的一个锚点，标识了某个特定的位置。

- 列出已有标签：

   以**字母顺序**列出标签

  ```
  $ git tagv1.0v1.1Copy
  ```

- 轻量标签（lightweight）：

  - 只是某个特定提交的引用，没有注释，只有一个标签号

  - 格式：

    ```
    ## 没有指定 commit 会默认在当前 HEAD 指向提交上打上标签$ git tag <tagname> [<commit>]Copy
    ```

- 附注标签（annotated）：

  - 是存储在 Git 数据库中的一个**完整对象**，可以被校验的，包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU Privacy Guard（GPG）签名与验证。
  - 在运行 tag 命令时**指定 -a 选项**，并使用 `-m` 选项指定了一条将会存储在标签中的**信息**。

- 显示标签对应信息：

  ```
  $ git show v1.0tag v1.0################################################ 附注标签才有的信息Tagger: ailanxier <ailanxier@ailanxier.cc>Date:   Sat May 3 20:19:12 2014 -0700tag information################################################ 附注标签才有的信息commit ca82a6dff817ec66f44342007202690a93763949Author: ailanxier <ailanxier@ailanxier.com>Date:   Mon Mar 17 21:52:11 2008 -0700    commit informationCopy
  ```

- 推送标签到远程：

   **默认**情况下，`git push` 命令并不会传送标签到远程仓库服务器上。在创建完标签后必须**显式**地推送标签到共享服务器上，就像共享远程分支一样。

  ```
  $ git push origin <tagname>$ git push origin --tagsCopy
  ```

   通常使用带有 `--tags` 选项的 `git push` 命令，会把**所有不在远程仓库上的标签**全部推送，不会区分轻量标签和附注标签。

- 删除标签：

  ```
  $ git tag -d <tagname>Copy
  ```

   这**只会删除本地标签**，如果已经推送到远程仓库了，此时删除标签是一件对别人不太友好的事，可以使用如下命令删除远程仓库的标签：

  ```
  $ git push origin -d <tagname>Copy
  ```

在已经推送标签后，最好不要使用 `-f` 选项强制替换已有标签，也不要删除标签。

- 可以根据标签名，使用 `git checkout` 切换到标签所在的提交，但是此时通常会处于**分离 `HEAD` 状态**，即新的修改提交不能被分支记录，要索引到这一提交只能通过校验和。所以如果要进行更改，比如修复旧版本中的错误，那么通常需要创建一个新分支，可以使用切换并新建分支的命令：

  ```
  $ git checkout -b      bugFix_v2.0.0     v2.0.0Copy
  ```

## Git 分支

### 分支简单操作

- 新建分支：

  ```
  $ git branch <branchname> [<start-point>]Copy
  ```

  如果不指定 `<start_point>` 则在 `HEAD` 指向的提交对象上创建一个指针，即为分支，同时在 `.git\refs\heads` 上新建一个名为 `branchname` 的文件，这个文件只含有 40 位校验和。所以创建分支是很轻量的一个操作。该命令**不会切换分支**。

  `HEAD` 是一个特殊的指针，告诉 Git 当前在哪个分支上，且只会指向**本地分支**。当使用 `git checkout` 到一个远程分支时，会处于 `HEAD` 分离状态。

- 删除分支：

  ```
  $ git branch [-d | -D] <branchname>Copy
  ```

  `-d` 不能删除未合并的分支，`-D` 可以强制删除这种分支。

- 查看分支：

  - 不带选项和参数的 `git branch` 命令：

    ```
    $ git branch* dev     mynew   new   Copy
    ```

    显示所有本地分支，`*` 的分支为 `HEAD` 指向的分支。

  - `-v` 选项：

    ```
    $ git branch -v* dev   e31e199 [ahead 9] Merge  mynew 6a1cba4 test merge branch  new   6a1cba4 test merge branchCopy
    ```

    显示更详细的信息，如分支指向的提交记录**校验和**，与远程分支的**差异**，提交记录的**描述信息**。

  - `-vv` 选项：

    ```
    $ git branch -vv* dev   e31e199 [o/dev: ahead 9] Merge  mynew 6a1cba4 [o/new] test merge branch  new   6a1cba4 [o/new] test merge branchCopy
    ```

    显示**跟踪的远程分支**，用的较多。

  - `-a` 选项：

    ```
    $ git branch -a* dev  mynew  new  remotes/o/dev  remotes/o/newCopy
    ```

    列出所有分支，包括远程分支。

  - `--merged` 和 `--no-merged` 选项：

    ```
    $ git branch [--merged | --no-merged] [<branchname>]Copy
    ```

    显示已（未）合并到**当前分支** 或 `<branchname>`（在有该参数的情况下）的分支。一般已合并的分支，如果其为临时分支，**可以删除**。

- `git branch -f`：

  这可以当做一个新建分支命令，也可以**移动现有分支到指定位置**，格式为：

  ```
  $ git branch -f <branchname> [<start-point>]Copy
  ```

### Git merge 🏳️‍🌈

- 通常使用的格式：

  ```
  git merge [--squash] [--no-commit] [<commit>]Copy
  ```

  假设分支情况如下：

  ```
     A---B---C topic /D---E---F---G *masterCopy
  ```

  在 `master` 分支合并 `topic` 分支，即执行 `git merge topic` 后，分支情况如下：

  ```
     A----B----C topic /             \D---E---F---G---H *masterCopy
  ```

  会得到一个合并的提交，它有**两个父提交**。

- 解决冲突：

  - 合并时，如果两个分支修改了同一个文件，可能会出现冲突，Git 不能智能合并，只能手动解决冲突，提示如下：

    ```
    CONFLICT (content): Merge conflict in d1.txtAutomatic merge failed; fix conflicts and then commit the result.Copy
    ```

    Git 会暂停下来，等待手动解决合并产生的冲突。在合并冲突后的任意时刻使用 `git status` 命令来查看那些因**包含合并冲突而处于未合并**（unmerged）状态的文件：

    ```
    $ git statusOn branch devYou have unmerged paths.  (fix conflicts and run "git commit")  (use "git merge --abort" to abort the merge)Unmerged paths:  (use "git add <file>..." to mark resolution)        both modified:   d1.txtno changes added to commit (use "git add" and/or "git commit -a")Copy
    ```

  - Git 会在有冲突的文件中加入**标准的冲突解决标记**，他们会包含一些**特殊区段**，格式如下：

    ```
    <<<<<<< HEADnow_change=======new_change>>>>>>> newCopy
    ```

    这表示 `HEAD` 所指示的版本（在运行 `merge` 命令之前已经切换到这个分支）在这个区段的上半部分（`======` 之上），而 `new` 分支所指示的版本在 `======` 之下。 为了解决冲突，你必须选择使用由`======` 分割的两部分中的一个，或者也可以自行合并这些内容。

    > 可以使用 VSCode 打开冲突文件，会有特殊的高亮和提示解决冲突，非常友好。

  - 在解决了所有文件里的冲突之后，对每个文件使用 `git add` 命令来将其标记为**冲突已解决**。此时可以使用 `git commit` 来生成合并提交，完成合并操作。

- 快进 `Fast-forward` 现象：

  - 当试图合并两个分支时， 如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候，只会简单的将指针向前推进，因为这种情况下的合并操作没有需要解决的分歧，所以也叫 `Fast-forward`。
  - 例如在 `dev` 分支成功合并 `new` 后，若切换回 `new` 分支再合并 `dev`，则只会将 `new` 分支移动到 `dev` 分支，Git 显示此时发生了 `Fast-forward`。

- `--squash` 选项：

  接受被合并的分支上的所有工作，并将其压缩至一个变更集，产生一个**不是合并提交**的提交记录。它**只有一个父提交**，但却引入另一个分支的所有改动，可以在记录一个新提交前做更多的改动。

  即好像合并了，但是没完全合并，只是把**另一个分支的变动**拿过来合并到工作目录中而已，另一个分支不会发生任何变化。

- `--no-commit` 选项：

  即将完成合并前，**强制停下**（否则会直接生成合并提交记录），允许用户进行修改和暂存，最后**手动提交**来结束合并。可以和 `--squash` 同时使用。

- `git merge --abort` 结束合并命令：

  当合并因为 `--no-commit` 或者冲突停下时，可以使用此命令结束合并，恢复到 Git 没执行合并命令之前的状态。

### Git rebase ♐

- 常用格式：

  ```
  $ git rebase [-i] [--onto <newbase>] [<branch>]Copy
  ```

- 演示过程：

  假设分支情况如下：

  ```
        A---B---C *topic     /D---E---F---G masterCopy
  ```

  在 `topic` 分支处执行 `git rebase master`，或者在其他任何地方执行 `git rebase master topic`，会得到一样的结果：

  ```
                A'--B'--C' *topic             /D---E---F---G masterCopy
  ```

  `git rebase master topic` 其实会先切换到 `topic` 分支，再执行 `git rebase master`。

- 上例中 `rebase` 原理：

  - 先找到这两个分支（即当前分支 `topic`、变基操作的目标基底分支 `master`） 的**最近共同祖先** `E`
  - 对比当前分支相对于该祖先的历次提交，提取相应的**修改**并存为临时文件，将当前分支指向目标基底 `G`
  - 最后以临时文件的修改按顺序应用于 `G` 上，就好像他们是在 `G` 后完成的修改，他们的校验和会发生改变，所以在图中用带 `'` 的字母表示。
  - 原来的提交仍然存在，但是只能通过提交的**校验和**访问，可以**使用 `git reflog` 查看** `HEAD` 移动的历史记录，找到已经不在历史中的提交校验和。

- 这样做的好处：

  - 确保在向远程分支推送时能保持**提交历史的整洁**。
  - 例如向某个其他人维护的项目贡献代码时，先在自己的分支里进行开发，当开发完成时你需要先将你的代码变基到**远程分支**上，然后再向主项目提交修改。
  - 这样的话，该项目的维护者就不再需要进行整合工作，只需要快进合并（`Fast-forward`）便可。

- 注意一种特殊情况：

  - 当**目标基底分支**已经包含了**当前分支**所做的**相同修改**（整个提交做的修改**完全相同**，可能是修复了同样的 bug），类似：

    ```
          A---B---C *topic     /D---E---A'---F masterCopy
    ```

    `A` 和 `A'` 所做的修改相同

  - 此时运行变基命令，会得到：

    ```
                   B'--C' *topic              /D---E---A'---G masterCopy
    ```

    `A` 的提交被跳过了，Git 认为 `G` 已经包含了 `A` 中的修改，就不会再重新做一次了。

- `--onto` 选项：

  - 个人认为比较危险，容易**丢失修改和新文件**，需要对分支的改变非常熟悉才建议使用。

  - 假设当前分支情况如下：

    ```
    o---o---o---o---o  master         \          o---o---o---o---o  next                           \                            o---o---o  topicCopy
    ```

    `topic` 分支依赖实现了某种功能的 `next` 分支，此时 `master` 分支的功能逐渐完善，已经可以让 `topic` 分支基于更稳定的 `master` 分支了，此时仅想保留 `topic` 分支中的修改，不需要 `next` 分支中的修改，则可以使用：

    ```
    $ git rebase --onto master next topico---o---o---o---o  master        |        \        |         o'--o'--o'  topic         \          o---o---o---o---o  nextCopy
    ```

    如果 `next` 分支中有新文件，且 `topic` 分支中没有对新文件进行修改，则变基后**新文件将消失**。

    同理如果 `topic` 中没有对 `next` 修改过的文件进行修改，变基后修改会消失。

  - 该选项可以用来去掉一段**连续的提交**，但是不太推荐，还是用 `-i` 选项的 `rebase` 比较好，也能达到同样的效果，但是更安全。

- **非常推荐的交互式变基** `-i` 选项：

  - Git 会打开一个文件（如果是 VSCode 打开会有图形操作界面，如下图）并列出将要被复制到目标分支的备选**提交记录**，以及它们的哈希值和**提交说明**，有助于理解这个提交进行了哪些更改。

    [![img](https://ailanxier.top/img/blog/git-note-images/vscode-rebase.png)](https://ailanxier.top/img/blog/git-note-images/vscode-rebase.png)

  - 可以根据意愿调整提交记录：

    - 调整提交记录的**顺序**（VSCode 通过鼠标拖放来完成）
    - 删除不想要的提交
    - 合并提交，有多种合并方式。
    - 修改提交说明

  - **具体选项**如下：

    - `p, pick <commit> = use commit` 完整保留提交。
    - `r, reword <commit> = use commit, but edit the commit message` 同 `p`，只是会打开编辑器修改**提交说明**。
    - `e, edit <commit> = use commit, but stop for amending` 同 `p`，同时等待手动 `git commit --amend`，修改到满意了就会运行 `git rebase --continue` 继续变基下一个提交。
    - `s, squash <commit> = use commit, but meld into previous commit` 合并提交到自己的夫提交，会打开文本编辑器修改**提交说明**。这个选项用的比较多。
    - `f, fixup <commit> = like "squash" but keep only the previous commit's log message` 同 `s`，但是舍弃这次提交的提交说明，其实和 `s` 的区别就在于它不会打开编辑器。
    - `d, drop remove commit` 舍弃提交。

- 变基是一个提交一个提交进行处理的。如果中间发生冲突也需要一个一个处理，此时 Git 会停下变基，用户可以打开**冲突文件**进行修改，修改完后用 **`git add .`** 添加所有的冲突文件，然后用 **`git rebase --continue`** 继续进行变基。

  Git 全程有命令提示，过程很友好。

  如果想终止变基，在 Git 停下阶段使用 `git rebase --abort` 恢复到变基前状态。

- 多次 `rebase` 实例：

  - 来自 [Git Reference](https://git-scm.com/docs) 的 `level advanced1` 关卡。

    这个网站很好玩，通过关卡学习 Git 分支常用操作，不涉及暂存区，有命令条数的要求（不严格）。

    一些常用命令：

    - `levels` 重新选择关卡
    - `reset` 将分支恢复关卡开始前原状
    - `show solution` 显示答案，建议不管有没有过关都显示一次答案

    它还提供了「沙盒」功能，可以提供用户使用各种具有破坏性的 Git 命令。

  - 原分支情况：

    [![img](https://ailanxier.top/img/blog/git-note-images/rebase1.png)](https://ailanxier.top/img/blog/git-note-images/rebase1.png)

  - 目标分支情况：

    [![img](https://ailanxier.top/img/blog/git-note-images/rebase2.png)](https://ailanxier.top/img/blog/git-note-images/rebase2.png)

  - 执行过程：

    1. `git rebase main bugFix`，得到 `C3'`。
    2. `git rebase bugFix side`，得到 `C4',C5',C6'`。
    3. `git rebase side another`，仅得到 `C7'`，这里其实对应之前说的特殊情况，`C4,C5` 和 `C4',C5'` 所作的修改是一样的，所以 `rebase` 不会将它们再放到这条提交线上。
    4. `git rebase another main`，利用快进 `Fast-forward`，使 `main` 分支更新到最新版本。

### Merge vs. Rebase

- 关于使用 `merge` 和 `rebase` 的争论一直存在，甚至有人说无脑用 `rebase` 就行，笔者认为还是看情况用比较好。

- `merge` 保留了所有提交记录，但是分支错杂，记录树非常乱；`rebase` 可以任意修改提交记录，记录树很整洁。

- 有一种观点认为，仓库的提交历史即是记录实际发生过什么。它是针对历史的文档，本身就有价值，不能乱改。 从这个角度看来，改变提交历史是一种亵渎，你使用「谎言」掩盖了实际发生过的事情。如果由合并产生的提交历史是一团糟怎么办？既然事实就是如此，那么这些痕迹就应该被保留下来，让后人能够查阅。

- 另一种观点则正好相反，他们认为提交历史是项目过程中发生的事。 没人会出版一本书的第一版草稿，软件维护手册也是需要反复修订才能方便使用。 持这一观点的人会使用 `rebase` 及 `filter-branch` 等工具来编写故事，怎么方便后来的读者就怎么写。

- 书上推荐的原则：

  只对尚未推送或分享给别人的本地修改执行变基操作清理历史，从**不对已推送至别处的提交执行变基**操作。

  这一原则极其重要，否则合作者可能会使用到已被变基放弃的旧提交，产生非常混乱的结果

## Git 远程协作

### 远程仓库

- 显示远程仓库的 URL：

   显示需要读写远程仓库的**简写**与其对应的 URL。

  ```
  $ git remote -vorigin  git@github.com:ailanxier/Test.git (fetch)origin  git@github.com:ailanxier/Test.git (push)Copy
  ```

- 添加远程仓库：

  ```
  $ git remote add <shortname> <url>Copy
  ```

 之后可以用字符串 `shortname` 来代替整个 URL。

- **联网**查看远程仓库的详细信息 🧙‍♂️：

  ```
  $ git remote show origin* remote origin  URL: https://github.com/my-org/project  Fetch URL: https://github.com/my-org/project  Push  URL: https://github.com/my-org/project  HEAD branch: master  Remote branches:    master            tracked    dev-branch        tracked    issue             new (next fetch will store in remotes/origin)  Local branches configured for 'git pull':    dev-branch merges with remote dev-branch    master     merges with remote master  Local refs configured for 'git push':    dev-branch        pushes to dev-branch             (up to date)    master            pushes to master                 (up to date)Copy
  ```

   同样会列出远程仓库的 URL 与**跟踪分支**的信息，在特定的分支上执行 `git push` 会自动地推送到哪一个远程分支，哪些远程分支不在你的本地，哪些远程分支已经从服务器上移除了，还有执行 `git pull` 时哪些分支会自动合并

- 移除远程仓库：

  ```
  $ git remote rm <shortname>Copy
  ```

- 重命名远程仓库：

   将远程仓库 `a` 重命名为 `b`。

  ```
  $ git remote rename a bCopy
  ```

### 远程分支

- 远程分支的**命名格式**为 `<remote name>/<branch name>`，`<remote name>` 常见如 `origin`，`<branch name>` 常见如 `master`。

  `origin` 和 `master` 并**无特殊含义**，即不是关键词，可以改成任意名字。

  `master` 是运行 `git init` 时默认的起始分支名字，原因仅仅是它的广泛使用，`origin` 是当你运行 `git clone` 时默认的远程仓库名字。

- 拉取到本地的远程分支，只能通过 `git fetch`，`git pull` 和 `git push` 移动，当 `HEAD` 切换到远程分支时通常会处于 `HEAD` 分离状态。

- 本地**跟踪分支**（**上游分支**）：

  - 跟踪分支是与远程分支有直接关系的本地分支。

  - 如果在一个**跟踪分支**上输入 `git fetch`，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。

    在一个跟踪分支输入 `git push`，如果和远程分支**不同名**，不能自动识别出要推送到哪个分支。

  - **新建**跟踪分支的命令：

    ```
    $ git checkout -t <remote>/<branch>Copy
    ```

    新建一个名为 `branch` 的分支，跟踪远程分支 `<remote>/<branch>`。

    如果想**自定义**分支名字，可以使用：

    ```
    $ git checkout -b <branchname> <remote>/<branch>Copy
    ```

  - 为**当前本地分支**设置（修改）上游分支：

    ```
    $ git branch -u <remote>/<branch>Copy
    ```

    上游快捷方式：

    当设置好跟踪分支后，可以通过简写 `@{u}` 来引用它的上游分支。

    例如在 `master` 分支时并且它正在跟踪 `origin/master` 时，可以使用 `git merge @{u}` 来取代 `git merge origin/master`。

  - 可以使用 `git branch -vv` 查看本地分支及其跟踪分支，属于**离线操作**，即没有联网。

### Fetch,Pull,Push 👨🏻‍💻

- `git fetch`：

  - 一般格式：

    ```
    $ git fetch [<remote>] [<branchname>]Copy
    ```

    从远程仓库 `<remote>` 上抓取本地没有的数据时，它并**不会修改工作目录**中的内容，只会修改远程分支。

  - 不指定 `remote` 和 `<branchname>`：

    默认从 `origin` 远程仓库获取数据更新**本地所有分支**。如果一个分支**已经设置了跟踪分支**，则**该分支**从那个跟踪分支所在仓库获取数据。

  - 指定 `branchname`：

    仅更新指定远程分支。

  - `git fetch --all`：

    从**所有远程仓库**下载本地没有的数据。

- `git pull`：

  - 可以理解为 `git fetch` + `git merge` 的缩写：

    ```
    $ git pull origin foo############################## 等价于下面两条命令$ git fetch origin foo$ git merge origin/fooCopy
    ```

  - 需要注意最终 `merge` 的位置，无论执行 `git pull` 前处于哪个分支，这个命令都会合并到**这个分支**上，这个分支**可能并不跟踪远程分支** `foo`。所以笔者更推荐`git fetch` 后手动 `git merge`。

  - `git pull` 例子：

    - 原分支情况：

      ```
            A---B---C master on origin     /D---E---F---G *master	⬆	origin/master in your repositoryCopy
      ```

    - 运行 `git pull` 后，分支情况：

      ```
            A---B---C origin/master     /         \D---E---F---G---H *masterCopy
      ```

  - 使用 `--rebase` 选项：

    不适用 `merge` 策略，转而执行 `git fetch` + `git rebase`。

- `git push`：

  - 一般格式：

    ```
    $ git push <remote> <branch>Copy
    ```

    推送本地 `branch` 的变更到远程仓库 `remote`，同时**本地的远程分支**会移动到 `branch` 的位置。

  - 神奇的 `<refspec>` 格式：

    - 使用 `git push origin a` 时，Git 自动将 `a` 分支名字展开为`refs/heads/a:refs/heads/a`， 那意味着「推送本地的 `a` 分支来更新远程仓库上的 `a` 分支」。
    - 这种带冒号的 `<src>:<dst>` 格式为 `<refspec>` 格式，`<src>` 可以是某个具体的提交的校验和，只要是 Git 能识别的都可以起效。
    - 这意味着上述语句等价于 `git push origin a:a`，所以我们完全可以使用 `git push origin b:a`，即使分支 `b` 和 `origin/a` 毫无关联，但是一般不会这样做。
    - 如果 `<dst>` 不存在，Git 甚至会在远程仓库新建一个名为 `dst` 的远程分支。
    - **如果 `<src>` 为空**，即使用 `git push origin :a`，会**删除**远程仓库中的 `origin/a` 分支
    - `git push` 和 `git fetch` 也可以使用这样的格式，但是觉得用处不大就不提了 👻

  - `--all` 选项：

    `git push` 默认是只推送当前分支，该选项会推送所有分支。

## Git 杂项

### 相对引用

- 在

   

  ```
  ^
  ```

   

  后面添加一个数字来指明

  哪一个

  父提交：

  - 通常用于合并提交节点，如果**只有一个父节点**，那么 `^` 就表示它的父节点。
  - 对于合并提交节点：
    - **第一父提交** `^1` 是**合并时所在分支**
    - 第二父提交 `^2` 是所合并的分支

- 另一种相对引用是使用 `~`，同样是指向**第一父提交**，因此 `HEAD~` 和 `HEAD^` 是等价的。而区别在于你在后面加数字的时候，例如 `HEAD~2` 代表「第一父提交的第一父提交」。下图可以更清晰地反映区别：

[![img](https://ailanxier.top/img/blog/git-note-images/relative.png)](https://ailanxier.top/img/blog/git-note-images/relative.png)

- `^` 和 `~` 前可以是**提交校验和**，**分支名**和 `HEAD` 等。

### 交互式暂存

- 使用 `git add -i`，发现 Git 新世界。

- Git 将会进入一个交互式终端模式，显示类似下面的东西：

  ```
  $ git add -i		staged 			unstaged 		path	1: 	unchanged 		+0/-1 			TODO	2: 	unchanged 		+1/-1 			index.html	3: 	unchanged 		+5/-1 			lib/simplegit.rb	*** Commands ***	1: [s]tatus  2: [u]pdate  3: [r]evert  4: [a]dd untracked	5: [p]atch   6: [d]iff    7: [q]uit    8: [h]elpWhat now>Copy
  ```

  首先显示 `HEAD` 和暂存区有差异的文件，将**暂存的修改**列在**左侧** `staged`，**未暂存的修改**列在**右侧** `unstaged`。

  同时会进入循环命令状态，直到输入 `7` 或 `q` 退出（8 个命令都可以输入**首字母**或数字生效）。

- `1 status`：

  - 显示 `HEAD` 和暂存区有差异的文件，和第一次显示时一样，且是**实时**的，如果你在 `git add -i` 之后做了修改可以通过 `status` 得知。
  - `staged` 的 `unchanged` 表示 `HEAD` 和暂存区的该文件相同，即还**未暂存**该文件。
  - `unstaged` 的 `nothing` 表示暂存区和工作目录的该文件相同，即**已暂存**该文件的全部修改。
  - 注意，这里不会显示**未跟踪的新文件**。

- `2 update`：

  - 它会问你想要暂存哪个文件，如果没有要暂存的文件（**全部已暂存**或**未修改**或**未跟踪**），则会直接退出，回到循环，否则显示：

    ```
    What now> u		staged 			unstaged 		path	1: 	unchanged 		+0/-1 			TODO	2: 	unchanged 		+1/-1 			index.html	3: 	unchanged 		+5/-1 			lib/simplegit.rbUpdate>>Copy
    ```

  - 如果要暂存 `TODO` 和 `index.html` 文件，可以输入数字或**区间**（本例中等价于输入 `1-2`），用**空格或逗号**隔开：

    ```
    Update>> 1,2		staged 			unstaged 		path   *1: 	unchanged 		+0/-1 			TODO   *2: 	unchanged 		+1/-1 			index.html	3: 	unchanged 		+5/-1 			lib/simplegit.rbUpdate>>Copy
    ```

    每个文件前面的 `*` 意味着**选中**的文件将会被暂存。

  - 如果要取消选中的 `TODO` 和 `index.html` ，使用 `-1 -2` 即可。

  - 如果在 `Update>>` 提示符后不输入任何东西并直接**按回车**，Git 将会暂存选择的文件。

- `3 revert`：

  - 显示已经**暂存的文件**，操作方式和 `2 update` 相同，选中并 `revert` 表示将**暂存区的该文件**恢复到 `HEAD` 状态，相当于 `git restore --staged 文件路径`。
  - 对于加入暂存区的新文件，该操作会将该文件重新变为 `untracked` 状态。

- `4 add untracked`：

  显示未跟踪的新文件，操作方式同上，操作相当于 `git add 未跟踪文件`。

- `5 patch`：

  高端操作，只暂存**某个文件**的部分修改，看起来挺复杂的，先不记了 🦥

- `6 diff`：

  显示已暂存的文件，操作方式同上，操作相当于 `git diff --cached`，比较的是 `HEAD` 和暂存区文件的差异。

- `7 quit`：润🏃🏻

当你在修改了大量文件后，希望这些改动能拆分为若干提交而不是混杂在一起成为一个提交时，交互式暂存会非常有用。

### Git stash 💾

- 应用背景：

  当你在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态，而这时你想要切换到另一个分支做别的事。问题是，Git 可能不允许在已修改的工作目录执行**切换分支**命令，你不想仅仅因为这些不成熟的修改创建一次提交。 针对这个问题的答案是使用 `git stash` 命令。

- `stash` 会处理工作目录的脏的状态（即**跟踪文件的修改**与**暂存区的改动**），然后将**未完成的修改**保存到一个栈上，而你可以在任何时候重新应用这些改动（甚至在**不同的分支**上）。

- 执行 `git stash` 或 `git stash push`（在不带选项和参数的情况下两者等价），将**工作目录和暂存区**的修改贮藏到栈顶，同时将这两棵树还原到 `HEAD` 状态。

- `git stash -m "描述"`，给这个贮藏记录像提交记录一样写描述文字

- `git stash -k`，在贮藏**工作目录和暂存区**的修改的同时，只还原工作目录到 `HEAD` 状态，保持暂存区修改不变。

- `git stash -u`，默认情况下，`git stash` 只会贮藏**已修改和暂存的已跟踪**文件。如果指定 `-u` 选项，Git 也会贮藏任何**未跟踪**文件。甚至对于**已忽略**的文件，使用 `-a` 选项也可以贮藏。

- `git stash list` 查看贮藏栈中的情况：

  ```
  stash@{0}: on master: using git stash -m message
  stash@{1}: WIP on master: c264051 Revert "added file_size"
  stash@{2}: WIP on master: 21d80a5 added number to log
  Copy
  ```

  栈顶为 `stash@{0}`，它使用了 `-m` 选项的贮藏命令。

  可以用 `stash@{n}` 来指代任意贮藏记录，在命令格式中使用 `<stash>` 表示。

- `git stash show [<stash>]`：

  显示某条贮藏记录的 `diff` 差异，不带 `<stash>` 参数时默认显示栈顶贮藏记录。

- `git stash pop [--index] [<stash>]`：

  - 删除栈顶或 `<stash>` 的贮藏记录，同时应用在当前**工作目录**。
  - 如果产生**冲突**，则需要在解决冲突后，使用 `git add .` 并 `git commit` 解决冲突状态。注意这种情况 Git **不会自动删除**栈中对应贮藏记录。可以使用 `drop` 删除（后面有讲）。
  - 使用 `--index` 选项，会将贮藏记录的修改应用到当前**暂存区**。

- `git stash apply [--index] [<stash>]`：

  除了**不删除**贮藏记录外，功能完全相同，用得较多。

- `git stash drop [<stash>]`：

  删除栈顶或 `<stash>` 的贮藏记录。

- `git stash clear`：

  删除所有贮藏记录。

- `git stash branch <branchname> [<stash>]`：

  - 对于一些会对当前节点**发生冲突**的贮藏记录，可以通过在**贮藏记录原本的提交节点**新建一个分支，应用贮藏的所有修改，进行测试处理。
  - 因为是在本来的节点上新建分支，所以是**不会发生冲突**的（本来就在这个节点基础上做的修改）。
  - 该命令会删除栈顶或 `<stash>` 的贮藏记录，并同时对**暂存区和工作目录**产生影响。

### Git clean 🧹

- 常用格式：

  ```
  $ git clean [-d] [-x | -X] -i [--] [文件路径]
  Copy
  ```

- 命令可以用于清理不在版本控制范围内的文件，即**未跟踪**的文件，但不清理**已忽略**的文件。

- 命令默认只清理 `.git` 所知文件夹下的文件，**不清理未跟踪文件夹**下的文件。

- 如果指定了**文件路径**，那么 `-d` 和 `-x | -X` 都不生效。

- `-d` 选项：

  递归进入**未跟踪的文件夹**下清理文件。

- `-x` 选项：

  清理范围包括**已忽略**的文件。

- `-X` 选项：

  清理范围**只包括已忽略**的文件。

- `-i` 选项：

  - 推荐使用该命令时都加上 `-i` 选项，使用交互式清理方式，一目了然要清理哪些文件。

  - 显示的交互界面如下：

    ```
    $ git clean -i
    Would remove the following item:
      a b c
    *** Commands ***
        1: clean                2: filter by pattern    3: select by numbers
        4: ask each             5: quit                 6: help
    Copy
    ```

  - `1 clean`：

    删除 `Would remove the following item:` 下的文件，并退出交互模式。

  - `2 filter by pattern`：

    列出当前删除范围内的文件，输入 `*.txt` 等文件类型进行过滤，过滤的文件**移出**删除范围：

    ```
    $ git clean -i
    Would remove the following items:
      1/1.md   1/1.txt
    *** Commands ***
        1: clean                2: filter by pattern    3: select by numbers
        4: ask each             5: quit                 6: help
    What now> 2
      1/1.md   1/1.txt
    Input ignore patterns>> *.md
      1/1.txt
    Input ignore patterns>>
    Would remove the following item:
      1/1.txt
    *** Commands ***
        1: clean                2: filter by pattern    3: select by numbers
        4: ask each             5: quit                 6: help
    What now>
    Copy
    ```

    输入 `*.md`，不删除 `1.md` 文件。

  - `3 select by numbers`：

    按顺序列出删除范围内的文件，通过数字进行选择哪些文件要删除：

    ```
    $ git clean -i
    Would remove the following items:
      1/1.md   1/1.txt
    *** Commands ***
        1: clean                2: filter by pattern    3: select by numbers
        4: ask each             5: quit                 6: help
    What now> 3
        1: 1/1.md     2: 1/1.txt
    Select items to delete>> 2
        1: 1/1.md   * 2: 1/1.txt
    Select items to delete>>
    Would remove the following item:
      1/1.txt
    *** Commands ***
        1: clean                2: filter by pattern    3: select by numbers
        4: ask each             5: quit                 6: help
    What now>
    Copy
    ```

  - `4 ask each`：

    逐个询问在删除范围内的文件，是否要删除，全部询问后退出交互模式：

    ```
    $ git clean -i
    Would remove the following items:
      1/1.md   1/1.txt
    *** Commands ***
        1: clean                2: filter by pattern    3: select by numbers
        4: ask each             5: quit                 6: help
    What now> 4
    Remove 1/1.md [y/N]? n
    Remove 1/1.txt [y/N]? y
    Removing 1/1.txt
    Copy
    ```

### Git Cherry-pick 🌸

- 命令格式很简单：

  ```
  $ git cherry-pick [-n] [-m] 可以多个<commit>
  $ git cherry-pick (--continue | --skip | --abort | --quit)
  Copy
  ```

- 拣选一个或多个提交的修改到 `HEAD` 下，例如原分支情况如下：

  [![img](https://ailanxier.top/img/blog/git-note-images/cherry1.png)](https://ailanxier.top/img/blog/git-note-images/cherry1.png)

   执行命令 `git cherry-pick C3 C4 C7` 后，分支变成：

  [![img](https://ailanxier.top/img/blog/git-note-images/cherry2.png)](https://ailanxier.top/img/blog/git-note-images/cherry2.png)

- 要求执行命令前保持 `working tree clean`，可能要像 `git merge` 一样合并冲突，合并后使用 `git add` 和 `git cherry-pick --continue` 生成新的提交。

- `-n` 选项：

  合并后不自动生成提交，并且退出 `CHERRY-PICKING` 状态，即不能再使用 `git cherry-pick [--continue | --skip | --abort | --quit]` 等命令，需要手动 `git commit` 生成提交。

- `-m` 选项：

  拣选不能选择合并提交的节点，除非使用 `-m <parent-number>`，`<parent-number>` 指的是哪个父节点，相当于之前说的相对引用 `^<parent-number>`

- `--continue` 都很熟悉了；`--skip` 跳过当前某个 `cherry-pick` 的提交；`--abort` 终止整个 `cherry-pick` 过程并还原到初始状态；`--quit` 退出 `CHERRY-PICKING` 状态，可以中途退出拣选过程，不完成整个过程。
