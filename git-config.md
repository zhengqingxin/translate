# Git 配置最佳实践

> 本文转载自：[众成翻译](http://www.zcfy.cc)
> 译者：[郑 farmer](http://www.zcfy.cc/@undefined)
> 链接：[http://www.zcfy.cc/article/3350](http://www.zcfy.cc/article/3350)
> 原文：[https://blog.scottnonnenberg.com/better-git-configuration/](https://blog.scottnonnenberg.com/better-git-configuration/)

我喜欢[Git](https://git-scm.com/)，[每天](https://github.com/scottnonnenberg/thoughts-system#other-files)都在使用它。正如我[最近所做的事情](https://blog.scottnonnenberg.com/eslint-part-1-exploration/#making-it-my-own)，花了一些时间通篇阅读文档，并检查我的全局 Git 配置。欢迎阅读[stack improvements](https://blog.scottnonnenberg.com/tags/stack-improvements/)系列第四篇文章。

## 一切都是Git

我开始写代码的[时代](https://blog.scottnonnenberg.com/2017-twenty-years-online/)还非常古老，那时候使用文件复制和[Visual SourceSafe](https://en.wikipedia.org/wiki/Microsoft_Visual_SourceSafe)的方式进行源码管理。即使如此，我对源代码管理的概念也是非常吃惊的，我居然可以坐在家里撸代码。

后来，在加利福尼亚大学，在项目中我遇到 [Concurrent Versions System (CVS)](https://en.wikipedia.org/wiki/Concurrent_Versions_System)，那时只有一些小项目，所以我当时并没有很好的理解它。

在微软工作的几年里，我使用 [Team Foundation Server](https://en.wikipedia.org/wiki/Team_Foundation_Server) 进行代码管理，当时有个新名词“App Week”，指的是新接触 Visual Studio 的人将花费一整个星期的时间熟悉该产品，以确保它能正确工作。而在那段时间里，我所有的个人项目都是使用 [SVN](https://en.wikipedia.org/wiki/Apache_Subversion)。它是免费而且容易在本地运行。通过它可以跟踪我所有的本地代码变化。

2010年秋天的时候，我在学习 [Ruby on Rails](http://rubyonrails.org/) 来开发一个[项目](https://scottnonnenberg.com/work/#stark-raving-bits-2010-q-3-to-2011-q-2)，通过查看教程，介绍了[Heroku](https://www.heroku.com/) 和一个新的源代码管理系统：**Git**。这是令人吃惊的 - 我可以像它在本地托管一样对待它，同时也可以与他人互动。没有锁定，离线可用，智能合并。我爱上她了。

从此 Git [火了](https://rhodecode.com/insights/version-control-systems-2016)。它成为了开源的标准。它在各种开源托管平台中使用。有许多GUI工具支持 - 专用的源代码控制工具以及代码编辑器。

所以，懂她非常重要。

## 全局配置

不管你知不知道，其实你都已经有了一份 Git 全局配置。它是你 home 目录中的 `.gitconfig` 文件。大多数`.gitconfig`文件都包含你的用户名和电子邮件地址，是你在[getting started with Git](https://help.github.com/articles/set-up-git/)过程中创建的。其实在这个文件中还有[更多的配置项](https://git-scm.com/docs/git-config#_variables)。

我的整个 .gitconfig 可以[通过这里查看](https://gist.github.com/scottnonnenberg/fefa3f65fdb3715d25882f3023b31c29)。需要的话可以直接到那里查看，不过本文我们将更详细地讨论每个部分。

### Alias

.gitconfig 中有趣的部分是 [alias 部分](https://git-scm.com/docs/git-config#git-config-alias)，您可以在其中创建自己的命令。感觉默认命令满足不了需求？在这里添加。有什么你不习惯的？在这里添加自己的版本吧！

*   `prune = fetch --prune` - 当在其他人将分支推送到远程仓库时，我也会得到了大量的本地分支。[Prune](https://git-scm.com/docs/git-fetch#git-fetch--p)可以删除远端已经删除的任何本地分支。配置在这里，因为我总是忘记它。

*   `undo = reset --soft HEAD ^` - 如果我在做出提交时犯了一个错误，这个命令会可以在提交之前撤销。通常我只是在这种情况下修改现有的提交，因为它保留了提交信息。

*   `stash-all = stash save --include-untracked` - 当你正在开发，有人频繁的要求你切换分支时，[stash](https://git-scm.com/docs/git-stash) 是非常有用的。这个命令确保当你 stash 时，可以记录没有被 `git add` 的新文件。

*   `glog = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)%Creset'` - 使用[默认 git log](https://git-scm.com/docs/git-log) 查看提交历史记录有时是无效率的，并没有真正关注最重要的信息。这种彩色的图形展示更容易理解，特别是当分支复杂的时候。

###Merge###
没有合并提交，没有合并两个历史记录，只是平滑的在两次提交之中。

你可能会想知道如何完成这项工作。答案用 [git rebase](https://git-scm.com/docs/git-rebase)，用于把一个分支的修改合并到当前分支，它[非常有用](https://nathanleclaire.com/blog/2014/09/14/dont-be-scared-of-git-rebase/)

当我 pull 时与 `master` 有冲突的时候，我使用这种方式来处理。当你在本地分支上修改后，同时其他人在 `master` 上 做了修改，我想这样比你直接 merge 到你本地分支时的 commit 更好。

这样你可以避免多出一个 merge 的 commit。如果我打算新建一个merge commit，我可以用明确的 git merge -ff 来创建。

在发生合并冲突时，`conflictstyle = diff3` 会给您更多的信息。

### Commit

`gpgSign = true` 确保您的所有 commit 都由你的 [GPG](https://www.gnupg.org/) 密钥签名。这通常是一个好主意，因为 `.gitconfig` 文件中没有验证您的用户信息，这意味着看起来像您这样的提交可能会轻松显示在其他人的提交 信息中。

事实上，我曾经用过别人的凭据，因为帐户和机器配置耗时太长。我的提交请求是通过别人的帐号提交的，但内部的所有提交都是我的真实账号。

[将你的 GPG key 添加到 Github](https://help.github.com/articles/adding-a-new-gpg-key-to-your-github-account/)并尝试一次提交，你可能就会解决你现在的疑问，您提交内容将会有[一个“已验证”标记](https://github.com/scottnonnenberg/eslint-config-thehelp/pull/5/commits)。

注意：

*   如果您有多个 GPG 密钥，可以使用 [user.signingKey](https://git-scm.com/docs/git-config#git-config-usersigningKey) 选项指定要使用的密钥。

*   上述的配置在 GUI 工具里不会生效，你需要在工具里的设置里找配置项。

*   `gpg-agent`可以保存口令，让我们更方便。所以使用它吧！

### Push

`default = simple`可能是你已经设置的[配置项](https://git-scm.com/docs/git-config#git-config-pushdefault)。它可以更轻松地将您的本地分支推送到远程，当二者分支名一样的时候。

`followTags = true`很简单。配置它以后，当你 `git push` 的时候可以直接将本地的 tags 提交到远程，而不用每次都加参数 `--follow-tags`。不知道你是不是和我一样，我如果创建了一个tag，我就基本上一定会将它推到远程的。

### Status

`showUntrackedFiles = all`通常当您添加一个新目录，但是还没有使用`git add`时，你用`git status` 将只显示目录名称。这困扰我很多次了，因为一个新的，很大的一个目录目录却只显示一行。[此选项](https://git-scm.com/docs/git-config#git-config-statusshowUntrackedFiles)在 `git status` 的时候显示该新目录下的所有文件。

注意：当仓库很大的时候，这可能会导致效率比较慢。

### Transfer

`fsckobjects = true`告诉Git，您希望在接收或发送修改时进行一些额外的检查。为什么要检查？毕竟发现错误赶早不赶晚！

注意：这可以使速度慢一些。

### Diff 工具：icdiff

除了内置的 git diff 命令之外，Git 还允许您指定一个[外部工具](https://git-scm.com/docs/git-difftool)来显示您的文件差异。下面配置可以配置默认使用 icdiff 显示存储库的两个文件之间的差异：

    [diff]
      tool = icdiff
    [difftool]
      prompt = false
    [difftool "icdiff"]
      cmd = /usr/local/bin/icdiff --line-numbers $LOCAL $REMOTE

你可以像正常情况那样使用它：git difftool master branch

`icdiff`很有趣，因为它试图在控制台中生成多彩的 GitHub 风格的差异。比通常的基于块的差异样式[更容易阅读](https://blog.scottnonnenberg.com/top-ten-pull-request-review-mistakes/#3-unified-diffs)。

注意：

*   你可能安装 icdiff 有遇到一些问题。令人高兴的是，有一个简单的[解决方法](https://github.com/jeffkaufman/icdiff/issues/72)。

*   将 `git diff` 当做备胎， `- icdiff` 似乎不会处理与`/dev/null`的比较。例如，在你添加一个新文件之后尝试 `git difftool --cached`。

## Bonus: More revisions!

你经常会用 git checkout master，对吧？在该命令中，`master`是 [revisions](https://git-scm.com/docs/gitrevisions) 的示例，是引用 master 分支中最新提交的简写。这些是常见的 revision 格式：

    # Check out a branch
    git checkout branchname

    # Check out a remote branch
    git checkout remotes/origin/branchname

    # Check out a specific commit
    git checkout 158e4ef8409a7f115250309e1234567a44341404

    # Check out most recent commit for current branch
    git checkout HEAD

但事实证明，有一种语言来指定revisions。所有这些操作都适用于以上使用的任何名词：

    # ^ means 'first parent commit,' therefore the second-most recent commit in the master branch
    git checkout master^

    # If it's a merge commit, with more than one parent, this gets the second parent
    git checkout master^2

    # Same thing as three ^ characters - three 'first-parent' steps up the tree
    git checkout master~3

    # The first commit prior to a day ago
    git checkout master@{yesterday}

    # The first commit prior to 5 minutes ago
    git checkout master@{5.minutes.ago}

您可以在这里找到所有支持的 revision 格式：[https：//git-scm.com/docs/gitrevisions。](https://git-scm.com/docs/gitrevisions)我很惊讶它是多么全面！

记住，在一个revision 中你可以使用大多数的 git 命令，比如：`git glog master@{10.days.ago}..master`

## 动起来吧！

可以从我的`.gitconfig`开始，或者定制你自己的风格。你甚至可以像我一样更加深入了解命令列表和选项列表，更多的分享出去。

使 Git 对你最有用。Git 为减少 bug 以及解决代码重提提供了非常大的舒适性。动起来吧！

* * *

一些很好的资源：

*   [这里](https://git-scm.com/docs/git-config#_variables)的配置需要具体项目具体分析。在项目配置文件中使用相同的格式：`project-dir / .git / config`

*   获取所有 `git log` 配置的最新信息：[https：//git-scm.com/docs/git-log#_options](https://git-scm.com/docs/git-log#_options)

*   各个版本 Git ：[https：//git-scm.com/docs/gitrevisions](https://git-scm.com/docs/gitrevisions)

*   了解签名：[https://mikegerwitz.com/papers/git-horror-story](https://mikegerwitz.com/papers/git-horror-story)

*   自定义别名：[https：//hackernoon.com/lesser-known-git-commands-151a1918a60](http://https：//hackernoon.com/lesser-known-git-commands-151a1918a60)

*   有关 git 仓库损坏：

    *   [https://groups.google.com/forum/#!topic/binary-transparency/f-BI4o8HZW0](https://groups.google.com/forum/#!topic/binary-transparency/f-BI4o8HZW0)
    *   [http://git.661346.n2.nabble.com/propagating-repo-corruption-across-clone-td7580504i40.html](http://git.661346.n2.nabble.com/propagating-repo-corruption-across-clone-td7580504i40.html)
*   对 Git 的复杂性感到沮丧？猛击这里：[https://stevebennett.me/2012/02/24/10-things-i-hate-about-git/](https://stevebennett.me/2012/02/24/10-things-i-hate-about-git/)