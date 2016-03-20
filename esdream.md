---
title: git使用笔记
tags: git,github
---
 1. git仓库是git的工作区，但仓库内.git文件不属于工作区。.git文件内为stage(又称index，git暂存区)和master(主分支)。
通过以下命令将修改添加到stage.
    ```
    $git add 文件名
    ```
    通过以下命令将修改从stage提交到master上。
    ```
    $git commit -m "提交说明"
    ```
2.  通过以下命令可以查看当前git仓库内的状态（是否有文件改变未add，是否有文件未commit）
	```
    $git status
    ```
 3. 查看命令历史
    ```
    $git reflog
    ```
4. 查看提交历史
    ```
    $git log
    ```
5. 回退到上一版本
    ```
    $git reset --hard HEAD^
    ```
    或回退到指定版本
    ```
    $git reset --hard 版本号
    ```
6. Git跟踪管理的是修改，而不是文件。
7. 每次修改，如果不add到暂存区，就不会加入到commit中。
8. 当你修改了工作区的某个文件的内容，想直接丢弃工作区的修改时，可以使用命令
    ```
    $git checkout -- filename
    ```
    当你修改了工作区内的文件内容，并添加到了暂存区，想丢弃修改时，第一步先使用命令
    ```
    $git reset HEAD filename
    ```
    然后再
    ```
    $git checkout -- filename
    ```
    *如果有多项修改（包括修改了某一个文件，增加/删除了文件，其中部分add部分没有add）没有提交，想要一并丢弃。可以使用以下两行命令*
    ```
    $git reset --hard  //已修改文件回退到上一次commit时的版本，删除新添加的已add的文件
    $git clean -xdf  //删除新添加的所有未add的文件
9. 删除git文件。分为两种情况：
	+ 使用以下命令删除文件，并commit:
      ```
      $git rm filename
      $git commit -m "remove test.txt"
      ```
      相当于在版本库和工作区中同时删除了该文件。这种情况下要恢复文件，只需回退到上一提交版本即可。
      ```
      $git reset --hard HEAD^
      ```
	+ 误删了文件，比如手动直接删除或在命令行中用```$rm filename```删除。这种情况下，相当于删除操作没有add,还未进入暂存区stage。这种情况下版本库中依然存在该文件，可以不用回退到上一版本，直接使用以下命令：
      ```
      $git checkout -- filename
      ```
  10. 将本地仓库添加到远层仓库
    第一步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
      ```
      $ssh-keygen -t rsa -C "youremail@example.com"
      ```
      创建完成后，会在用户主目录里找到.ssh目录(windows是在C:\用户\本机用户名文件夹下)。里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
          第二步：登录github，打开settings->"SSH Keys"页面，在里面添加id_rsa.pub文件的内容。
      然后就可以在github上创建与本地相同的仓库，并使用以下命令将本地的仓库同步到远程库上。
      ```
      $git remote add origin 远程库地址  //远程库地址在github对应远程库中
      $git push -u origin master  //-u参数会把本地master分支和远程的master分支关联起来，在以后的推送或者拉取时可以简化指令
      ```
      第一次push后，只要本地commit了，就可以直接以下命令直接将本地master分支的修改推送至github：
      ```
      $git push origin master
      ```
      或者
      ```
      $git push 
      ```
      \* *当远程库没有创建对应仓库时，本地库是无法push到远层库的*
11. 若已经有了远程库，想在其他设备上将远程库clone成本地库，只要正确配置ssh key后，进入想要clone远程库的目录，输入以下命令：
      ```
      $git  clone 远程库地址
      ```
12. 如果是本地库和远程库都存在，现在对远程库进行了修改，想同步到本地库。可以在本地库目录下使用以下命令同步：
    ```
    $git pull
    ```
13. 分支（branch）
查看分支：```$git branch```
创建分支：```$git branch 分支名```
切换分支：```$git checkout 分支名```
创建+切换分支：```$git checkout -b 分支名```
合并某分支到当前分支：```$git merge 分支名```
删除分支：```$git branch -d 分支名```
*注：*
+ 任何分支的修改都能提交到远程库中。但提交之前必须满足两个条件：第一，所有修改已经commit到该分支中。第二，必须通过以下命令将当前要提交的分支设置为上传流分支(upstream branch)：
  ```
  $git push --set-upstream origin 分支名
  ```
+ 次分支之间可以直接合并，只要在当前分支下输入```$git merge 要合并的分支名```即可。
14. ```git merge 分支```默认使用的是fast forward合并方式，这种模式在删除分支后，会丢弃分支信息。
    *fast forward合并方式*
    <img src="http://www.liaoxuefeng.com/files/attachments/00138490883510324231a837e5d4aee844d3e4692ba50f5000/0"></img>
		如果不希望丢弃分支信息，可以使用以下命令合并：
		```
        $git merge --no-ff -m "注释信息" 要合并的分支名
        ```
        *禁用fast forward的合并方式*
        <img src="http://www.liaoxuefeng.com/files/attachments/001384909222841acf964ec9e6a4629a35a7a30588281bb000/0"></img>
15. 当在工作过程中，有紧急的bug需要修复时，如果将当前工作add并commit，切换到bug分支上解决bug后再切回当前工作分支，那么log上会有大量不必要的记录。此时可以使用以下命令将当前工作分支暂时储存起来：
    ```
    $git stash
    ```
    可以多次将未提交的代码压入栈中，并使用以下命令查看栈中的保存的版本：
    ```
    $git stash list
    ```
    保存后的版本以以下形式出现：
    ```
    stash@{0} : WIP on 分支名: 上一次提交的版本号 add merge //WIP是work in process（半成品）的意思
    stash@{1} : WIP on 分支名: 上一次提交的版本号 add merge
    ```
    解决bug后，可以用以下命令恢复最近一次stash的内容：
    ```
    $git stash apply 
    ```
    如果想恢复某一次特定的保存版本，可以使用以下命令：
    ```
    $git stash apply stash@{栈下标}
    ```
    然后用```git stash drop```删除保存版本，同样加上```stash@{栈下标}```删除指定的保存版本。
    如果想恢复保存内容的同时直接将保存版本删除，可以使用以下命令：
    ```
    $ git stash pop stash@{栈下标}
    ```
    可以用以下命令直接删除所有保存内容：
    ```
    $ git stash clear
    ```
  16. 要删除没有合并的分支，可以使用以下命令强行删除：
      ```
      $ git branch -D 分支名
      ```
      *强行删除后，该分支将无法找回。*
  17. 可以用以下命令查看远程库的信息：
      ```
      git remote -v
      ```
        将打印：
        ```
      $ origin 远程库地址 (fetch)
      $ origin 远程库地址 (push)
        ```
  18. 本地对某个分支做了修改并提交，可以用以下命令向远程库推送分支：
      ```
      $ git push origin 分支名
      ```
19. 多人合作时，同伴从远程库clone时，默认情况下只能看到本地的master分支。如果想要将其他分支从远程库同步到本地。需要用以下命令：
    ```
    $ git checkout -b 分支名 origin/分支名
    ```
20. 当团队中一人向远程库推送了某一分支的新版本，另一人在其之后也尝试向远程库推送，则git会提示推送失败。解决方式如下：
    + 先尝试```$ git pull```同步。如果失败，则是没有指定本地dev分支与远程origin/dev分支的链接。需要输入以下命令创建：
		```
        $ git branch --set-upstream dev origin dev
        ```
        然后再```$ git pull```
    + 此时合并会有冲突，需要像分支管理的解决冲突那样解决。解决并commit后，再push：
		```
        $ git push origin dev
        ```
21. 可以使用以下命令为当前分支打一个标签，默认被打上标签的版本是```HEAD```(当前版本)：
    ```
    $ git tag 标签名
    ```
    可以使用```$ git tag```命令查看当前仓库所有分支上的所有标签。
    + *注：标签是按照字母先后顺序排列的*
   
	要给指定commit id（即版本号）的版本打上标签，指定标签名和说明文字等，可以用以下命令：
	```
	$ git tag -a 标签名 -m "说明文字" 版本号
    ```
    可以通过```-s```用私钥签名一个标签
    ```
    $ git tag -s 标签名 -m "说明文字" 版本号
22. 可以通过以下命令删除一个标签：
	```
    $ git tag -d 标签名
    ```
23. 标签默认打在本地库的分支中，可以通过以下命令将一个标签推送到远程库：
	```
    $ git push origin 标签名
    ```
	或者一次性推送所有尚未推送到远程的标签：
    ```
    $ git push origin --tags
    ```
    要通过命令删除远程库中的标签，需要使用以下命令：
    ```
    $ git push origin :ref/tags/标签名
    ```
    或者使用以下命令也可以：
    ```
    $ git push origin --delete 标签名
    ```
    删除远程库中的标签后，不要忘记删除本地库中的这个标签：
	```
    $ git tag -d 标签名
    ```
24. 需要忽略本地库中的某些文件时，需要编写.gitignore文件。文件中按以下代码形式书写需要忽略的文件：
	```
    # Windows:
    Thumb.db
    Desktop.ini
    
    # Python:
    *.so
    dist
    build
    ...
    ```
    不论提交与否（甚至还没add），.gitignore都能在本地库中实现忽略文件的效果，当然.gitignore依然应按照普通文件提交和同步的流程，推送到远程库上。
25. 使用以下命令为某个命令配置别名：
	```
    $ git config --global alias.别名 命令名 
	```
    例如：
    ```
    $ git config --global alias.co checkout //给checkout一个别名co
	```
    可以用字符串的形式，用一个单词作为一个长命令的别名，例如：
    ```
    $ git config --global alias.lg "log --color --graph --pretty=oneline --abbrev-commit"
    ```
    这样就能用```git lg```命令查看所有版本和分支的提交历史了。
26. 加上```--global```命令是对全局起作用，不加就只对当前仓库起作用。
	每个仓库的git配置文件放在.git/config中，别名在[alias]后面，要删除别名，只要直接删除别名所在行即可。
    而当前用户的git配置文件放在用户主目录下的.gitconfig文件中。
    配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。