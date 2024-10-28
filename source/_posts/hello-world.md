---
title: Hexo
date: 2020-01-06 15:00:51
tags: hexo
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

``` bash
创建一个新的提交
$ hexo new "My New Post"

Hexo启动
$ hexo server

pm2后台运行
$ pm2 stop all
$ pm2 start hexo_run js

生成静态页面
$ hexo generate

发布到远程站点
hexo deploy

//hexo_run.sh 命令
const { exec } = require('child_process')
exec('hexo server',(error, stdout, stderr) => {
        if(error){
                console.log('exec error: ${error}')
                return
        }
        console.log('stdout: ${stdout}');
        console.log('stderr: ${stderr}');

```

