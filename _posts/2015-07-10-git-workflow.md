---
layout: post
title:  常用的几种Git Workflow
tags:   blogging
image:  git-workflow-banner.svg
---

Atlassian的Git教程写的非常之好，[尤其是这篇讲解常用的几种Git Workflow的文章](https://www.atlassian.com/git/tutorials/comparing-workflows)写的
非常全面，例子也生动易懂，本文就是据此整理而成。

Git本身并不会强制你使用那种工作流程，这里介绍的四种常用Git工作流程只是一个起点，其中提到的都是一些指导性的方针
而不是具体的规则，我们只是想告诉你有那些可能方式，所以你可以从几种工作流程中找到符合你的实际需要的部分搭配使用。

{{ more }}

### 目录
{:.no_toc}

* Table of Contents Placeholder
{:toc}

-----

## 集中式工作流

![几种式工作流](/img/posts/git-workflow-01.svg)

切换到一个分布式的版本控制系统上工资看貌似是一个艰巨的任务，但你使用Git的时候也可以不改变现有的工作流程。你的团队完全可以像使用Subversion那样来开发项目。

但使用Git可以享受到SVN所没有的两个有点优点：

- 每个开发者可以得到整个项目的本地副本
- 可以方便的使用Git廉价的分支和合并功能

### 如何工作

和SVN一样，集中式工作流用一个中央仓库来记录项目的所有更改，所有的改动都会被提交到master这个分支上，就像SVN的trunk一样，
除此之外，不再需要任何其它的分支。

开发者先将中央仓库克隆到本地，然后在上面修改文件并提交这些改变，与SVN不同的是，这些改变和提交都是存储在本地的，
这时是不需要访问远程的中央仓库的，直到需要把这些提交都同步到中央仓库的时候，才需要远程连接。

等到想把这些改变发布到正式项目的时候，开发人员将把本地的master分支`push`到远程的中央仓库，这等同与SVN的`commit`操作，
但这一次操作添加的是在服务器master分支上还没有的所有的本地提交。

![几种式工作流的提交](/img/posts/git-workflow-02.svg)

#### 管理冲突

中央仓库代表正式项目，因此它的提交历史应该被视为神圣的和不可改变的。
如果一个开发者的本地提交偏离了中央存储库，Git会拒绝接受这些改变，因为这会改写正式项目的提交历史。

![几种式工作流的提交](/img/posts/git-workflow-03.svg)

开发者在发布他的新功能前，需要先拉取一下中央仓库上的改变到本地，并且把本地的改变放在远程改变的后面（我们称这个过程为 `rebase` ），
这好比是说“我想把我的改变添加到他人的改变的后面”，这样的结果会得到一个完全线性的历史，就像我们以前使用SVN那样。

如果本地的改变和远程的改变发生了冲突，Git会暂停这个`rebase`过程，给我们一个手工解决冲突的机会，
在解决冲突的过程中，使用常用的`git status`和`git add`的命令就可以。如果解决冲突的过程中遇到了困难，也可以完全放弃
这次`rebase`，回到起点重新开始或去寻求其他人的帮助。

### 示例

让我们一步步看看一个典型的小团队是如何用这种工作流来协作的。团队中有两个开发者，John和Mary，他们在独立地开发各自的功能，
然后通过中央仓库来分享他们的成果。

#### 一个人创建中央仓库

![创建中央仓库](/img/posts/git-workflow-04.svg)

首先得有个人来在服务器上创建一个中央仓库（空仓库或者从已有的Git或SVN仓库导入），中央仓库必须是`bare`类型的：

    ssh user@host git init --bare /path/to/repo.git

#### 每个人都克隆中央仓库

![克隆中央仓库](/img/posts/git-workflow-05.svg)

    git clone ssh://user@host/path/to/repo.git

当你克隆之后，Git会自动添加一个名为`origin`的快捷方式，它指向这个远程的“父”仓库。

#### John开发他的功能

![John在本地开发](/img/posts/git-workflow-06.svg)

在他的本地分支上，John可以用标准的Git提交流程来开发新功能——编辑（edit），缓存（stage），提交（commit）：

    git status # View the state of the repo
    git add <some-file> # Stage a file
    git commit # Commit a file</some-file>

由于这些都是在操作本地分支，John可以循环往复的执行上面的过程而不需要与远程仓库交互。当开发一个大的功能时，需要拆分成几个
小的模块时这非常有用。

#### Mary开发她的功能

![Mary在本地开发](/img/posts/git-workflow-07.svg)

和John一样，Mary在本地开发她的功能，这时他们互不干扰，因为本地分支是私有的。

#### John发布他的特性

![Mary在本地开发](/img/posts/git-workflow-08.svg)

一旦John完成了他的功能的开发，他就应该把他本地的提交都发布到中央仓库中去，以便其他成员可以访问：

    git push origin master

`origin`代表了一个中央仓库的远程连接，它是在John克隆它的时候自动创建的。`master`参数的意思是让`origin`的master分支
变得和本地分支一样。John的这次推送成功了，没有发生任何的冲突，因为从John克隆仓库的时候直到现在，中央仓库还未曾改变过。

#### Mary也试图发布她的特性

![Mary在本地开发](/img/posts/git-workflow-09.svg)

在John发布了他的功能以后，Mary也开发完成了，她试图用同样的方式来发布自己的功能：

    git push origin master

但是，由于她的本地历史和中央仓库的历史已经发生了偏差（现在中央仓库的状态和她克隆的时候已经不一样了，多了John发布的东西），
Git会拒绝这次推送：

    error: failed to push some refs to '/path/to/repo.git'
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
    hint: before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

这避免了Mary去改写正式仓库的历史。Mary需要拉取John更新的内容到她本地仓库，和她的本地提交合并，然后再尝试发布。

#### Mary在John发布的基础上做rebase

![Mary在本地开发](/img/posts/git-workflow-10.svg)

Mary使用`pull`命令来拉取John的更改，这很像SVN的`svn update`,把远程的更改拉取到本地仓库并和本地的提交合并。

    git pull --rebase origin master

`--rebase`这个选项告诉Git从远程中央仓库同步改变到master分支后，把Mary的本地提交移动到这些远程改变之后。如下图所示：

![Mary在本地开发](/img/posts/git-workflow-11.svg)

如该忘了加这个选项，会产生一个多余的“合并提交”，对这种工作流来说，最好使用`rebase`，而不是产生“合并提交”。

#### Mary解决冲突

![Mary在本地开发](/img/posts/git-workflow-12.svg)

如果Mary和John修改的是两个不相关的功能，一般也不会产生冲突。如果确实产生了冲突，Git会暂停当前的`rebase`操作，会显示下面的信息：

    CONFLICT (content): Merge conflict in <some-file>

![Mary在本地开发](/img/posts/git-workflow-13.svg)

这时，Mary只要运行`git status`命令就能看到哪里冲突了：

    # Unmerged paths:
    # (use "git reset HEAD <some-file>..." to unstage)
    # (use "git add/rm <some-file>..." as appropriate to mark resolution)
    #
    # both modified: <some-file>

Mary会编辑这些冲突的文件，然后缓存这些修改后的文件，并让`rebase`继续：

    git add <some-file>
    git rebase --continue

Git会继续处理下一个提交，如果出现冲突用相同的方式处理即可。

如果觉得实在无法解决冲突，可以执行下面的命令回滚这次`rebase`，重新回到执行`pull`前的状态：

    git rebase --abort

#### Mary成功发布她的功能

![Mary在本地开发](/img/posts/git-workflow-14.svg)

这时Mary可以很容易的发布自己的功能了：

    git push origin master

可以看到，使用Git也可以实现传统SVN方式的工作流程，但这并没有充分发挥Git分布式的特性。下一节我们会探讨功能分支工作流的使用方式。


## 功能分支工作流

![功能分支工作流](/img/posts/git-workflow-feature-01.svg)

相对与集中式的工作流，功能分支工作流更加鼓励开发者间的协作并简化他们间的通信。功能分支工作流的背后的核心思想是，所有的功能开发都
应该在一个专门的分支上，而不是在主分支上。这便于多个开发者在开发同一个分支又不会干扰主代码库，这意味着主分支永远不会包含还不能发布
的坏代码。这也非常有利于持续集成环境。

把功能开发独立为一个单独的分支，也能充分发挥Pull Request的作用，这可以让开发人员围绕这个分支进行讨论，在这个分支合并到正式项目前
也给了其他开发人员一个检视这个分支的机会。即使是在开发的过程中，遇到困难的时候，也可以发起一个Pull Request来征求其他成员的建议。
非常关键的一点是，Pull Request团队成员对彼此的工作进行评论变得非常容易。

### 如何工作

功能分支依然会使用中央仓库，master依然代表了正式项目的历史。但开发人员不再在本地的master分支上直接提交了，而是每次开发一个新功能
的时候都会新开一个功能分支，然后在这个功能分支上工作。功能分支应该有一个清晰的名称，比如`animated-menu-items`或`issue-#1061`。
其目的是每个分支的作用都是十分清晰且高度聚焦的。

在技术上，master分支和功能分支并没有本质的不同，所以像之前操作master分支一样，可以对功能分支进行编辑（edit），缓存（stage）和提交（commit）。

此外，功能分支应能够（也应该）被推送到中央仓库。这样多个人可以协作开发，且不需要去改动master的代码。master是唯一的“特殊”分支，
中央仓库可以有多个功能分支，服务器端的功能分支也是备份每个人本地提交的一种方式。

#### Pull Request

除了隔离功能的开发，功能分支也使得通过Pull Request来讨论变更成为可能。一旦有人完成了功能后，并不会马上合并到master，而是先把
功能分支推送到服务器上，并提出一个Pull Request，要求合并到master。在合并代码到正式代码库之前，这是一个开发者们审查代码变更的一个好机会。

虽然Code Review是Pull Request提供的最主要的一个好处，但实际上，Pull Request提供的是一种通用的讨论代码的方式。比如在功能开发的早期
就可以针对这个分支发起一个Pull Request以方便讨论（并非为了马上合并），需要寻求其他人帮助的时候也可以发起一个Pull Request，对此感兴趣
的人会收到通知，他们可以看到这些问题并给出有益的提交。

Pull Request一旦被接受，就要合并代码到master上。首先要先确保本地的master和中心仓库上的master同步，然后合并功能分支到本地的master上并
把本地的master推送到中央仓库的master分支上。

Git本身并不提供Pull Request功能，可以在GitHub，Bitbucket,Stash等产品上使用这个功能。

### 示例


## Gitflow工作流


## Forking工作流
