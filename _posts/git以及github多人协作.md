# git以及github多人协作

## 1、首先自己和自己的github建立链接

## 2、fork主仓库

[仓库地址](https://github.com/hello-zhanghao/MU-MIMO-UL-System)

![image-20201014161926455](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201014161926455.png)

然后自己账号下就有一个相同的仓库

## 3、建立自己的仓库链接以及和主仓库的链接

```shell
git init  # 初始化一个git 仓库
git remote add origin url    # 其中url应该换成自己的仓库地址
git remote add upstream url  # 这边url应该是主仓库地址
```

## 4、在本地新建分支-编辑文件-合并分支

```shell
git featch origin master:mybranch  # 从origin指向的仓库拉取master分支并修改为mybranch分支，这样本地就会有两个分支，一个master，一个mybranch
git checkout mybranch  			   # 切换到mybranch分支，然后就可以对文件进行修改操作
```

- 修改完文件后将变动提交到本地mybranch分支

```
git add .
git commit -m '代码改动说明'
```

- 合并到远程仓库的master分支（由于此时master分支可能已经被改动过）

```shell
# 由于此时master分支可能已经被改动过, 所以需要获取最新的代码，然后将其合并到自己修改的分支上
git featch upstream master:newmaster  # 获取最新的master分支并在本地新建一个分支newmaster
git merge newmaster                   # 将newmaster分支合并到当前分支，（如果没有冲突的直接合并，有冲突需要解决冲突）
```



## 5、推送本地分支到远程

```
git push                              # 将本地分支推送到自己的仓库
```

## 6、Pull request

- 修改完成后，去自己的github仓库发起pull request，将改动通知主仓库，然后主仓库将改动合并到最新的代码

## 7、git常用命令

```shell
git push -f origin master  # 强制覆盖掉
```



## 克隆仓库（）

1、在本地下载完git后，然后配置环境连接到自己的远程仓库（参见网上教程就行）

2、·找一个自己存放代码的位置，然后右键选择git bash here,然后输入命令克隆远程仓库

![捕获](git以及github多人协作.assets/捕获.PNG)

![image-20201021204037969](git以及github多人协作.assets/image-20201021204037969.png)

```shell
git clone https://github.com/hello-zhanghao/MU-MIMO-UL-System.git
  # 克隆远程仓
```





# pycharm连接github

![image-20201021211053705](git以及github多人协作.assets/image-20201021211053705.png) <img src="git以及github多人协作.assets/image-20201021211305892.png" alt="image-20201021211305892" style="zoom: 50%;" />

![image-20201021211407785](git以及github多人协作.assets/image-20201021211407785.png)

按照指示输入密码就行，最后pycharm出现下图就ok了

![image-20201021211546285](git以及github多人协作.assets/image-20201021211546285.png)

## 配置git（首先需要下载git）

![image-20201021211726488](git以及github多人协作.assets/image-20201021211726488.png)

## 拉取代码仓库

![image-20201022145307684](git以及github多人协作.assets/image-20201022145307684.png)

![image-20201022145548302](git以及github多人协作.assets/image-20201022145548302.png)

![image-20201022150006748](git以及github多人协作.assets/image-20201022150006748.png)

## pycharm打开该工程文件夹

![](git以及github多人协作.assets/image-20201022150442098.png)

## 开发流程

![image-20201022152115999](git以及github多人协作.assets/image-20201022152115999.png)

1. 工程文件拉取后，可以在pychram中的git看见本地的master分支和远测的master分支，并且可以看见他们的历史提交记录，刚开始时两个分支的提交记录是完全相同的
2. 在本地开发，开发完成后，如果想要提交，则点击右上角的commit，
3. ![image-20201022152840528](git以及github多人协作.assets/image-20201022152840528.png)![image-20201022153056623](git以及github多人协作.assets/image-20201022153056623.png)

4. 查看无误后，添加commit message然后点击commit提交
5. 然后可以发现本地的master分支多了一个提交记录

![image-20201022153426272](git以及github多人协作.assets/image-20201022153426272.png)

6. 如果想要将该分支push到远端仓库，首先要刷新远程仓库，如果发现远端仓库和直接clone时的仓库没有变化，则可以直接push,如果发生了变化，则需要先将远端分支merged到本地分支，然后解决冲突，合并后的程序无误后就可以push了
7. ![image-20201022153712462](git以及github多人协作.assets/image-20201022153712462.png)

## merge时冲突的原因

![image-20201022160103579](git以及github多人协作.assets/image-20201022160103579.png)

