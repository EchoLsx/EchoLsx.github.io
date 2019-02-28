---
title: zsh与git常用命令
catalog: true
date: 2018-12-29 11:21:06
subtitle:
header-img:
tags:
 - git
 - zsh
---


#### zsh中关于git的常用快捷键

#### Alias

|   zsh   |   git命令  |
| :-----: |  :-----:  |
|  gst    |  git status|
|  gd     |  git diff |
|  gaa    |  git add --all |
|  gcmsg  |  git commit -m |
|  gp     |  git push |
|  gl     |  git pll |
|  gcam   |  git commit -a -m |
|  gco   |  git checkout |
|  gcb   |  git checkout -b |
|  grh   |  git reset HEAD |



####  git常用命令


|   zsh   |   git命令  |
| :-----: |  :-----:  |
|丢弃文件的修改 |git checkout .  或者 git checkout xxxx|
|已经add了，要撤销add操作|  git reset HEAD 或者 git reset HEAD xxxx|
|只回退前1次commit的信息，保留修改代码 （一般用在commit备注写错了）|git reset --soft head~1|
|彻底回退到上次commit版本，不保留修改代码 |git reset --hard head^ (hard是危险操作，尽量不要用)|
|clone某个分支|  git clone [url] -b [branch]|
|git branch（-a）| 展示本地（所有）分支|
|git checkout -b dev-xxx| 本地创建一个dev-xxx分支|
|git checkout [name]| 本地分支切换到[name]分支|
|git branch -d [name]| 删除本地[name] 分支|
|git push origin test:test| 提交本地的test分支作为远程的test分支|
|git stash| 暂存当前修改内容 |
|git stash pop| 弹出修改内容 |
|git stash list| 查看保存的内容列表|


####  开发到一半，同步远端代码
```
git stash
git pull
git stash pop
```

#### 工作到一半，需要先修复之前的一个bug

当然你可以开一个新的分支，先把修复完的代码提交了，回头再merge自己本地的工作内容，但是操作起来会很麻烦

我们可以用 **git stash** 来简化这个流程
```
git stash
修复bug...
git commit -a -m "fix bug"
git stash pop
```

#### commit的emoji表情

| emoji |  代码 | 说明|
| :-----: |  :-----: | :-----: |
| 🎨调色板| :art:| 改进代码结构/代码格式|
|🔥火焰|:fire:| 移除代码或文件|
|bug| :bug:| 修复bug|
|火花|:sparkles:| 引入新功能|
|🔨锤子|:hammer:| 重大重构 |
|铅笔|:pencil:| 文档|
|💄口红| :lipstick:| 更新UI和样式|
|➕加号|:heavy_plus_sign:| 增加依赖|
|➖加号|:heavy_minus_sign:| 减少依赖|
|⬆️上升|:arrow_up:| 升级依赖 |


#### git commit 提交规范
| type | 说明 |
| :-----: |  :-----: |
|feat| 新功能|
|fix| 修复bug|
|doc| 文档改变|
|style| 样式改变（不影响js）|
|refactor| 某个已有功能重构|
|perf|性能优化|
|del| 删除文件|
|chore| 构建工具或构建过程变动 webpack|








