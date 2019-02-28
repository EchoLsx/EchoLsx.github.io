---
title: nrm
catalog: true
date: 2018-11-16 16:41:51
subtitle:
header-img:
tags: 
    - node
---

### 1.nrm是什么？

> nrm是npm资源管理器，允许你快速切换npm源。

顾名思义，就是说nrm是一个管理npm的工具。

### 2.nrm 安装
使用npm安装  npm install -g nrm  （-g 全局安装）

### 3.nrm 使用

- 查看配置
```
nrm ls
```
- 查看当前的npm源：
 ``` 
 nrm currnt
  ```

- 切换npm源
 nrm use [name]
```
 nrm use cnpm
```

- 新增私有源
nrm add [name] [私有npm地址]
```
nrm add company http://resgistry.npm.xxx.org
```

- 删除配置源
nrm del [name]

```
nrm del company
```




