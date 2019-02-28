---
title: mac 本地起SVN服务
catalog: true
date: 2018-10-08 18:50:19
subtitle:
header-img:
tags:
    - SVN
    - mac
---

嗯？ MAC居然自带SVN服务器？

```
svnserve --version

```
可以看到下面的信息
svnserve, version 1.10.0 (r1827917)
   compiled Aug 14 2018, 02:37:13 on x86_64-apple-darwin17.0.0...

#### 1.创建代码库

我们来创建一个代码库用于保存代码

在终端输入命令
```
sudo mkdir -p /Users/echo(根据自己的用户名修改)/svn/mycode    //创建了一个文件夹，这个文件夹路径可以自己随意设定

sudo svnadmin create /Users/echo(根据自己的用户名修改)/svn/mycode   //将之前创建的文件夹设置为svn的代码库
```

我们在Finder中打开上面的路径，我们可以开到其中生成了一些文件，我们需要配置conf文件夹下的文件

#### 2.配置svn用户权限

 - 1）配置svnserve.conf文件

打开conf目录下的svnserve.conf文件，将下列配置项前面的#和空格都去掉
```
# anon-access = read

# auth-access = write

# password-db = passwd

# authz-db = authz
```
- 2）配置passwd文件

打开conf目录下的passwd文件，仿照里面的注释格式，修改为如下：
```
[users]

echo = 123
```
- 3）配置authz文件
仿照里面的注释格式，在 [groups]下面添加组名和用户名，多个用户之间用逗号(，)隔开。修改为如下：
```
[groups]

users=echo,

[/]

@users=rw
```
rw为可读可写

#### 3、启动svn服务器

在终端中输入：svnserve -d -r /svn文件夹上层的路径/svn 【注意不是到mycode目录】


#### 4、尝试导入一个文件夹
```
svn import /Users/echo/workspace/code/study/svnapp svn://localhost/mycode/svnapp --username=lesixiang --password=123 -m "初始化导入"
```
如果提示： Can't open file '/Users/echo/svn/mycode/db/txn-current-lock': Permission denied
在命令行中输入：sudo chmod -R g+rws svn 即可 

#### 5、Cornerstone来连接svn
![1.jpg](1.jpg)


#### 6、运行代码验证

```js
case 'svn':
    const svnTemp = path.resolve(process.env.HOME, '.hik-svn-applist', url.replace(/\/|:/g, '-'))
    spinner.start()
    fse.emptyDirSync(svnTemp)
    let cmd2 = `svn checkout ${url} ${svnTemp}`
    execa.shell(cmd2).then(res => {
        spinner.stop()
        console.log('== 下载完成，开始安装... ==')
        resolve(svnTemp)
    }).catch(err => {
        spinner.stop()
        console.succeed('== 下载异常，停止安装 ==')
        reject(err)
    })
break
```

 