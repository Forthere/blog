---
title: git工作流
date: 2019-12-06 15:54:11
tags: git flow
---

### 目的
`
看了文章之后, 自己写一下加深印象。
`


### 感受
`
文章主要是通过指定分支规则来实现工作流
`

### master分支
`
主干，不做任何代码操作, 只合并dev分支或者Hotfix分支上面的代码, 每一次提交做一个tag。
`

### Hotfix
`
线上系统出现bug紧急修复。 修复完成之后合并到master和dev分支中
`

### dev 分支
`
开发主干, 不做任何代码操作， 合并rebase分支或者feature分支或者Hotfix上面的代码。 在有rebase分支的情况下, 不在做任何代码的合并操作。
`

### feature 分支
`
开发分支, 应该每一个新需求可以开一个分支。
`

### release 分支
`
线上发布分支, 给feature分支合并到dev发布到测试环境中, 如果出现问题在release中修改。
`

### git flow代码示例

```git
# 建立切换 dev分支
git branch -b dev
git push origin dev
# 建立切换 新需求分支
git branch -b feature dev
git push origin feature
# 一些改动
git status
git add some-file.js
git commit -m "添加一些文件"
# 完成feature --no-ff 禁止快进, 不会给feature的提交记录给带入合并的分支
git push origin dev
git checkout dev
git merge --no-ff feature
git push origin dev
git branch -d feature
# 开始 release
git branch -b release-0.0.1 dev
# 一些改动
git status
git add some-file.js
git commit -m "修复some-file"
git push origin release-0.0.1
# 完成 release
git checkout -b dev
git merge --no-ff release-0.0.1
git push origin dev

git checkout -b master
git merge --no-ff release-0.0.1
git push origin master

git branch -d release-0.0.1
git push origin --delete release-0.0.1

git tag -a v0.1.0 master
git push --tags
# 开始hotfix
git checkout -b hotfix-0.0.1 master
# 一些改动
git add some-file
git commit -m "修复some-file"
git push origin hotfix-0.0.1
# 完成hotfix
git checkout master
git merge --no-ff hoxfix-0.0.1
git push origin master

git checkout dev
git merge --no-ff hoxfix-0.0.1
git push origin dev

git branch -d hoxfix-0.0.1
git push origin --delete hoxfix-0.0.1

git tag -a v0.1.1 master
git push --tags
```

[**参考文档**](https://www.cnblogs.com/wish123/p/9785101.html)
