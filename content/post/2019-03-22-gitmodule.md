+++
draft = true
thumbnail = ""
tags = []
categories = []
date = "2019-03-22T13:04:48+09:00"
title = "2019 03 22 Gitmodule"
description = ""
+++



[blog] git submodule update                                                                                                             12:26:42  ☁  master ☂ ⚡
error: Server does not allow request for unadvertised object d16801abbd3dd1cf629999c6a867e99c1af14300
Fetched in submodule path 'themes/hugo_theme_robust', but it did not contain d16801abbd3dd1cf629999c6a867e99c1af14300. Direct fetching of that commit failed.
[blog] git submodule sync                                                                                                               12:26:50  ☁  master ☂ ⚡
Synchronizing submodule url for 'themes/hugo_theme_robust'
[blog] ls themes/hugo_theme_robust                                                                                                      12:29:25  ☁  master ☂ ⚡
[blog] git submodule update                                                                                                             12:29:32  ☁  master ☂ ⚡
error: Server does not allow request for unadvertised object d16801abbd3dd1cf629999c6a867e99c1af14300
Fetched in submodule path 'themes/hugo_theme_robust', but it did not contain d16801abbd3dd1cf629999c6a867e99c1af14300. Direct fetching of that commit failed.
[blog]  cd themes/hugo_theme_robust                                                                                                     12:29:41  ☁  master ☂ ⚡
[hugo_theme_robust] git submodule sync

[blog] git status                                                                                                                       12:31:58  ☁  master ☂ ⚡
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
  (commit or discard the untracked or modified content in submodules)

	modified:   themes/hugo_theme_robust (new commits, modified content)

no changes added to commit (use "git add" and/or "git commit -a")



[blog]                                                                                                                                  12:26:40  ☁  master ☂ ⚡

[blog]                                                                                                                                  12:26:41  ☁  master ☂ ⚡
diff --git a/themes/hugo_theme_robust b/themes/hugo_theme_robust
index d16801a..e7a073f 160000
--- a/themes/hugo_theme_robust
+++ b/themes/hugo_theme_robust
@@ -1 +1 @@
-Subproject commit d16801abbd3dd1cf629999c6a867e99c1af14300
+Subproject commit e7a073f0a31ee990b641824e7864c9edbf7191e5-dirty



https://qiita.com/kinpira/items/3309eb2e5a9a422199e9#%E3%81%BE%E3%81%A8%E3%82%81


[blog] git submodule                                                                                                                   12:47:18  ☁  master ☀ 𝝙 𝝙
 e7a073f0a31ee990b641824e7864c9edbf7191e5 themes/hugo_theme_robust (heads/master)


---------------------------------

[blog] git submodule                                                                                                                       12:59:27  ☁  master ☀
-d16801abbd3dd1cf629999c6a867e99c1af14300 themes/hugo_theme_robust

 [blog] git submodule init                                                                                                                  12:57:46  ☁  master ☀
Submodule 'themes/hugo_theme_robust' (https://github.com/eichisanden/hugo_theme_robust.git) registered for path 'themes/hugo_theme_robust'
[blog] git status                                                                                                                          12:57:49  ☁  master ☀
On branch master
Your branch is up-to-date with 'origin/master'.

nothing to commit, working tree clean
[blog] git submodule update                                                                                                                12:57:59  ☁  master ☀
Cloning into '/Users/eichi.mita/src/github.com/eichisanden/work/blog/themes/hugo_theme_robust'...
error: Server does not allow request for unadvertised object d16801abbd3dd1cf629999c6a867e99c1af14300
Fetched in submodule path 'themes/hugo_theme_robust', but it did not contain d16801abbd3dd1cf629999c6a867e99c1af14300. Direct fetching of that commit failed.
[blog]



[blog] git submodule                                                                                                                    12:58:26  ☁  master ☂ ⚡
+e7a073f0a31ee990b641824e7864c9edbf7191e5 themes/hugo_theme_robust (heads/master)


[blog] cd themes/hugo_theme_robust

[hugo_theme_robust] git checkout e7a073f0a31ee990b641824e7864c9edbf7191e5                                                                13:00:23  ☁  master ☂ ✖
Note: checking out 'e7a073f0a31ee990b641824e7864c9edbf7191e5'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at e7a073f... Update README.md

$ git diff

diff --git a/themes/hugo_theme_robust b/themes/hugo_theme_robust
index d16801a..e7a073f 160000
--- a/themes/hugo_theme_robust
+++ b/themes/hugo_theme_robust
@@ -1 +1 @@
-Subproject commit d16801abbd3dd1cf629999c6a867e99c1af14300
+Subproject commit e7a073f0a31ee990b641824e7864c9edbf7191e5



[blog] git submodule                                                                                                                     13:06:10  ☁  master ☂ ✚
 e7a073f0a31ee990b641824e7864c9edbf7191e5 themes/hugo_theme_robust (heads/master)

 