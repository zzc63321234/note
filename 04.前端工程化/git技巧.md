- 本地仓库同时推送到多个远程仓库

  - 方法一: 使用 git remote add 命令

    1# 本地仓库与远程仓库关联起来，再查看一下远程仓库情况

    git remote add origin https://gitee.com/fox9916/gaoxTest.git

    git remote -v

    2# 使用如下命令再添加另一个远程仓库

    git remote add sundyn http://192.168.100.54/git/gaoxing/gaoxTest.git

    3# 再次查看远程仓库的情况，可以看到已经有两个远程仓库了。然后再使用相应的命令 push 到对应的仓库就行了。这种方法的缺点是每次要 push 两次。

     git  push origin master:master

    git  push sudnyn master:master

  - 方法二: 使用 git remote set-url 命令

    1#删除方法一的 sundyn 远程仓库。

    git  remote rm sundyn

    2#使用如下命令添加远程仓库。

    git remote set-url --add origin http://192.168.100.54/git/gaoxing/gaoxTest.git

    3#查看远程仓库情况。可以看到 github 远程仓库有两个 push 地址。这种方法的好处是每次只需要 push 一次就行了。

    git remote -v

    git push origin master:master