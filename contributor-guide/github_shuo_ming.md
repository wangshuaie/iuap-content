# gitHub使用说明 #


## git如何处理别人的pull request及解决冲突

出过两次了，每次都查很多资料，太蛋疼，记录在此。

当你的项目比较牛逼的时候，有人给你贡献代码，但他修改的地方恰恰你前阵子也修改了，这样在github中就不能够自动merge了。

因此你需要手动去解决冲突。首先要在本机安装好命令行工具gitbash，之后用clone拉下你的项目，之后

按照以下命令输入

```
git checkout -b 某人-master master
git pull https//github.com/某人的/某项目的.git master
```

这时候命令行会提示你有冲突，冲突文件是啥，那如何解决冲突呢，很简单

在同步代码的过程中，git会自动检查冲突，并尝试进行**自动合并**。最好的情况应该是大家同时修改一个文件，但是大家修改的地方不同了。在这样的情况下，git会进行非冲突合并，这时，在调用 git pull 的时候，git会尝试进行非冲突合并。 
而在合并过程中有冲突的时候， git 会把修改记录直接保存在文件中，让开发者判断文件如何解决合并。例如，在一个描述文件中同时修改了一句话，在合并的时候，git会这么做：

```
<<<<<<< HEAD
It's not a project cool enough for you to enjoy the code but a mix of my thoughts in the year 2012~2013. I didn't know where the project leads to. Hope it will became useful after practice.
=======
It's not a project
```

即把两个更改都写在文件上，但是用=======来区别发生冲突的位置，在=======以上是 HEAD，即本地的代码；而=======以下则是来自远程的更改了。这个时候，你可以选择保留远程或本地的修改或者都不要（简单地说，把不需要的内容删除即可）。

也就是说我们把文件修改好后，把增加的那几行head >>><<<之类删掉就ok啦。之后冲突修改完毕，我们继续输入

```
git commit -a //把修改提交到这个人的分支上，会提示你成功merge本地代码到这个人的代码库

git checkout master //切换到自己的分支上

git merge 某人-master //
```

还要记着一点，本地修改代码前一定要先pull一下看看，记得慎用github的在线编辑功能