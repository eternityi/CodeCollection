# Git 的基础命令

### Blob

![](../.gitbook/assets/image%20%287%29.png)

你可能没怎么见过‘blob’这个单词，但它确是 **git 中最基本的数据存储结构。**每次文件改变，Git 直接存储文件内容，这在git术语里被称作 Blob。  
  


### **4254a17be3（哈希）**

![](../.gitbook/assets/image%20%289%29.png)

你看到的这些数字是内容的 [SHA1 哈希](https://link.zhihu.com/?target=http%3A//en.wikipedia.org/wiki/SHA1)，这些哈希值在git中作为每个对象的唯一标识，这些对象可能是commit，tree，或者 blob。哈希由 40 个字符构成，但仅仅需要前几个字符（在 Github 中是 10 个）就足够成为唯一的标识。

### **Tree（即文件夹或目录）**

![](https://pic4.zhimg.com/80/f028b27fd979023ce85992d91c0f72ff_hd.jpg)

在Git术语中树就像文件夹或者目录。通常在你的git库的顶部有一个树，用来记录哪些文件（又名 blobs ）和文件夹（又名 trees ）的位置。  
每个树都有一个哈希和内容信息  
了解更多 [Git Object: tree \| http://GitGuys.com](http://www.gitguys.com/topics/git-object-tree/)

### **Commit \(即快照\)**

![](https://pic4.zhimg.com/80/2b5880a136cab4fb3e795ee4b5020617_hd.jpg)

  
一个更加详细的 commit，tree 和 blob 结构。  
![](https://pic3.zhimg.com/80/170b089faab83718db2e1fb889d9bfa6_hd.jpg)

可以把 Commits 想象成一个快照：它们记录某个时间点相对应树的样子。每次提交会有一个哈希，提交者信息，时间以及注释。  
  
了解更多 [Git Object: Git Commit - GitGuys](http://www.gitguys.com/topics/git-object-commit/)  
  


### **Check in = 做一次提交**

提交的另一种叫法。

**Commit History（提交历史）**  
![](https://pic1.zhimg.com/80/f25313fd56e8d0395d963c2ae9d83cc0_hd.jpg)![](https://pic4.zhimg.com/80/5c2ae0d43f0a1cba4c85936a878aa03f_hd.jpg)  
  
如果你做了一些改变并且提交，这次提交保存了一个指向上一次提交的指针。所以提交“流”是单向的。提交“流”可以被分割为**分支。**  
  


### **Branch（即另存为）**

![](https://pic4.zhimg.com/80/cf3a43f87dc17b89a0559c9015d82377_hd.jpg)\*\*\*\*

**一个分支是一系列的提交，这个分支可以分成更多的分支。**默认分支名是 master，确切的说仅仅是一个可移动的指针。起初，会有一个 master 分支指向了你的最后一次提交。当你每一次提交，指针会自动的移动去指向最后一次提交。  
  
**和真正的分支一样，它也可以被进一步的拆分。**你可以在每一个分支上独立的工作。比如，同一时刻使用 Windows XP 和 Windows 7 在不同的版本下相互独立的进行工作。可以把一个基本的 Linux 拥有各种不同的风格想象成不同分支。  
  
你可以使用‘git show-branch’命令形象化地看到和下图类似的[提交和分支](http://www.gitguys.com/topics/git-show-branch-to-see-branches-and-their-commits/)。  
![](https://pic2.zhimg.com/80/54183528b3127adeb156ec9d451e2905_hd.jpg)

在这张图里绿色，蓝色，黄色和紫色的线表示了使用软件可视化后的不同分支。  
  
更多详细[Creating And Playing With Branches \| Git Branch \| GitGuys](https://link.zhihu.com/?target=http%3A//www.gitguys.com/topics/creating-and-playing-with-branches/)  
或者[What a Branch Is](https://link.zhihu.com/?target=http%3A//yyliang.cn/book/ch3-1.html)  
  


### HEAD

![](https://pic1.zhimg.com/80/09ea20f08564acc9d9786161a463598c_hd.jpg)  
**Git中的 HEAD 是指向当前分支引用的指针，**相应地也就是一个指向你的最后一次提交的指针。通常可以简单的认为 **HEAD 就是你的最后一次提交的快照。**  
  


### Tag（书签）

![](https://pic3.zhimg.com/80/4f1c491fb2ca395da349986f8af014f2_hd.jpg)  
分支可能变得复杂，tag 在这里可以拯救你。**Tag 就像一个书签。**创建后可以指向任何分支的任意地方的一次特定的提交。不像分支，比如 master 分支指向了最后一次提交，而 tag 通常可以指向特定的一次提交。  
  
一个 tag 代表了特定分支在某一个时刻的特定版本。  
  
  
更多详细[Git Object: Tag \| Git Tag \| GitGuys.com](https://link.zhihu.com/?target=http%3A//www.gitguys.com/topics/git-object-tag/)  
\[资源： [Git objects: the tag](https://link.zhihu.com/?target=http%3A//365git.tumblr.com/post/497500602/git-objects-the-tag)\]  


### Index（ 暂存区）

![](https://pic3.zhimg.com/80/ad9016850f8e8cb354a247a623679afa_hd.jpg)  
  
**这里的 Index 就是你打算的下一次提交。**从根本上来说它就是一个**装卸码头**你可以决定哪些改动被运走。由于工作目录和 Git 已经保存的内容在本质上是分离的，这允许开发者创建他们想要的提交。既然在 Git 保存数据之前有了这一个暂存区，你获得了更多的灵活性和控制权。  
  
  
更多详细[What is the deal with the Git Index? What is the Git Index? - GitGuys](https://link.zhihu.com/?target=http%3A//www.gitguys.com/topics/whats-the-deal-with-the-git-index/)  
  


### Checkout（检出）

![](https://pic2.zhimg.com/80/3ef0e5a9a09105344763d34905819459_hd.jpg)  
检出一个提交使得整个工作目录都匹配那个提交。这可以被用于查看你项目的一个旧的状态而不必用任何方式替换你当前的状态。检出一个文件让你看到那个指定文件的一个旧版本，使得你的工作目录处于没有被碰过的状态。  
  
  
大多数的图片和文本摘取自\[[git ready " learn git one commit at a time](https://link.zhihu.com/?target=http%3A//gitready.com/)\] 和 \[[Pro Git - Pro Git Book](https://link.zhihu.com/?target=http%3A//yyliang.cn/)\] ，并且被我公开的编辑和投递过。  
  
  
一些容易理解的有用链接  
[Aha! Moments When Learning Git](https://link.zhihu.com/?target=http%3A//betterexplained.com/articles/aha-moments-when-learning-git/)  
[Git documentation and Git tutorials on GitGuys \| GitGuys.com](https://link.zhihu.com/?target=http%3A//www.gitguys.com/topics/)  
  
原文：[How do you explain basics of Git in simple words?](https://link.zhihu.com/?target=http%3A//www.quora.com/How-do-you-explain-basics-of-Git-in-simple-words)

