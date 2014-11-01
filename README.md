# Explanation
This is an illustration a example of a rebase-based workflow, with the following activity:

1. a feature branch off of the upstream master
2. a new commit on master ('e') which is not yet on the feature branch
3. two (pair) programmers working **concurrently** on topic branches off of the
   upstream feature branch
4. a commit on the feature branch ('d') which is not yet on the topic branches
5. a rebase of the feature branch onto master, so it will include the new commit
   ('e') from the upstream master.
6. a rebase of both topic branches onto the (rebased) upstream feature branch, so
   they each will include commit 'e' from the upstream master branch and
   commit 'd' from the upstream feature branch.

# The Important Point Being Illustrated
Notice that in #6, the topic branches can be successfully rebased onto the upstream
feature branch, **even though the SHAs of their parent commits on the feature
branch have changed due to being rebased**

# Branch state before rebases
![branch state before rebases](https://github.com/thewoolleyman/git-rebase-workflow-example1/blob/master/git-rebase-workflow-example1-before-rebases.png)

# Explanation of Rebases
----
Review the state of the master branch and feature branch (#1,#2,#4):
```
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

$ git log --pretty=format:"%h%x09%s"
d251b93 add e on master
23c95f3 Initial commit

$ git checkout feature
Switched to branch 'feature'
Your branch is up-to-date with 'origin/feature'.

$ git log --pretty=format:"%h%x09%s"
b569a40 add d on feature
d855c01 add a on feature
23c95f3 Initial commit
```
----
Rebase the feature branch onto master (#5):
```
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: add a on feature
Applying: add d on feature

$ git log --pretty=format:"%h%x09%s"
ffafdad add d on feature
dbcf8b1 add a on feature
d251b93 add e on master
23c95f3 Initial commit
```
----
Review the state of the first concurrent topic branch (#3):
```
$ git checkout feature-topic1
Switched to branch 'feature-topic1'
Your branch is up-to-date with 'origin/feature-topic1'.

$ git log --pretty=format:"%h%x09%s"
549bf25 add b on feature-topic1
d855c01 add a on feature
23c95f3 Initial commit
```
----
Rebase the first concurrent topic branch onto the (rebased) feature branch (#6)
**Notice that this works even though the SHA of 'add a on feature' has changed
from d855c01 to dbcf8b1 as a result of the rebase of the feature branch.**
```
$ git rebase feature
First, rewinding head to replay your work on top of it...
Applying: add b on feature-topic1

$ git log --pretty=format:"%h%x09%s"
89dbcdf add b on feature-topic1
ffafdad add d on feature
dbcf8b1 add a on feature
d251b93 add e on master
23c95f3 Initial commit
```
----
Review the state of the second concurrent topic branch (#3):
```
$ git checkout feature-topic2
Switched to branch 'feature-topic2'
Your branch is up-to-date with 'origin/feature-topic2'.

$ git log --pretty=format:"%h%x09%s"
b49b067 add c on feature-topic2
d855c01 add a on feature
23c95f3 Initial commit
```
----
Repeat for the second concurrent topic branch...
Rebase the second concurrent topic branch onto the (rebased) feature branch (#6)
**Notice that this works even though the SHA of 'add a on feature' has changed
from d855c01 to dbcf8b1 as a result of the rebase of the feature branch.**
```
$ git rebase feature
First, rewinding head to replay your work on top of it...
Applying: add c on feature-topic2

$ git log --pretty=format:"%h%x09%s"
69186a0 add c on feature-topic2
ffafdad add d on feature
dbcf8b1 add a on feature
d251b93 add e on master
23c95f3 Initial commit
```
----
Push all rebased branches to remote:
```
$ git push --force --all
Counting objects: 12, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (8/8), 752 bytes | 0 bytes/s, done.
Total 8 (delta 5), reused 7 (delta 4)
To git@github.com:thewoolleyman/git-rebase-workflow-example1.git
 + b569a40...ffafdad feature -> feature (forced update)
 + 549bf25...89dbcdf feature-topic1 -> feature-topic1 (forced update)
 + b49b067...69186a0 feature-topic2 -> feature-topic2 (forced update)
```
----

# Branch state after rebases
![branch state after rebases](https://github.com/thewoolleyman/git-rebase-workflow-example1/blob/master/git-rebase-workflow-example1-after-rebases.png)
