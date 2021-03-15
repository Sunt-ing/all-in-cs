过完年，我开始重新工作学习了，在解决了一些琐事后，发现我之前提交给Apache Doris的一个PR出现了conflicts需要解决。对于我这种git菜鸡，这着实是个小挑战，于是我便开始着手解决它了。

我的本地仓库中如下：

```shell
D:\open_source\incubator-doris>git remote -v
origin  https://github.com/sunt-ing/incubator-doris.git (fetch)
origin  https://github.com/sunt-ing/incubator-doris.git (push)
upstream        https://github.com/apache/incubator-doris.git (fetch)
upstream        https://github.com/apache/incubator-doris.git (push)
```

要做的就是 rebase apache/incubator-doris 的master分支，解决相应的冲突。搞了半天，我学到的东西如下：

- rebase 和 merge 是两种对立的方式，二选一。如果选择merge，那么就会有一个merge的记录；如果选择rebase就不会有，但是有可能会出现一些问题。通常我们在master分支等公共分支上不会使用rebase，否则从这个分支分出去的一些分支可能就找不到自己的来源了。
- 要解决冲突，需要我们按照git给的提示，在有冲突的文件有冲突的行中二选一，删除不要的那一个版本。然后不断 "git add/rm <conflicted_files>", then run "git rebase --continue"。选择完后，git commit -am "conflicts solved" 一下，就可以push了。
- 遇到一堆骚操作把事情搞砸了，就git reset --hard <commit_id> 回退到正常的那个版本，回退后git push --force，然后再试一次。反正没什么问题。