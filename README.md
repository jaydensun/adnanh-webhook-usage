# adnanh-webhook-usage
测试webhook接收器框架： webhook (https://github.com/adnanh/webhook)

### 安装
官网下载最新版本

添加hooks.json，并按需修改。这里是配置了一个简单的打印http信息的功能
![image](https://user-images.githubusercontent.com/4156504/150673296-a12383ab-1b9a-4c6e-ad78-0b920574a2ac.png)

echoparam为接收到请求时执行的命令。详见源文件。

目录结构：
```
[root@VM-0-114-centos webhook-linux-amd64]# ll
total 12304
-rwxr-xr-x 1 root    root          19 Jan 23 17:18 echoparam
-rw-r--r-- 1 root    root         754 Jan 23 17:55 hooks.json
-rwxrwxr-x 1 ftpuser ftpuser 12590216 Dec  6  2020 webhook
[root@VM-0-114-centos webhook-linux-amd64]# 
```

启动： 
```
[root@VM-0-114-centos webhook-linux-amd64]# ./webhook -hooks hooks.json -verbose
[webhook] 2022/01/23 18:02:23 version 2.8.0 starting
[webhook] 2022/01/23 18:02:23 setting up os signal watcher
[webhook] 2022/01/23 18:02:23 attempting to load hooks from hooks.json
[webhook] 2022/01/23 18:02:23 os signal watcher ready
[webhook] 2022/01/23 18:02:23 found 1 hook(s) in file
[webhook] 2022/01/23 18:02:23   loaded: simple-one
[webhook] 2022/01/23 18:02:23 serving hooks on http://0.0.0.0:9000/hooks/{id}

```
启动日志中有服务地址（替换IP为公网IP，id对应配置中的id）
如： http://106.52.xx.xx:9000/hooks/simple-one

### 在 github 上尝试使用 webhook
在github上找一个repository，并进行配置
![image](https://user-images.githubusercontent.com/4156504/150673914-f8977155-67ae-4552-b75d-1da4481dd043.png)


### 测试
对repository进行操作（如提交代码）后，在repository设置中可以看到回调信息
![image](https://user-images.githubusercontent.com/4156504/150673657-0ab6155c-e99b-46cd-9521-4435667d23fc.png)

还可以查看具体的回调详情
![image](https://user-images.githubusercontent.com/4156504/150673962-c9bb05d1-dd8f-4050-a0a9-2201ebcd8a09.png)

在webhook日志也可以看到有请求被处理
```
[root@VM-0-114-centos webhook-linux-amd64]# ./webhook -hooks hooks.json -verbose
[webhook] 2022/01/23 17:46:45 version 2.8.0 starting
[webhook] 2022/01/23 17:46:45 setting up os signal watcher
[webhook] 2022/01/23 17:46:45 attempting to load hooks from hooks.json
[webhook] 2022/01/23 17:46:45 os signal watcher ready
[webhook] 2022/01/23 17:46:45 found 1 hook(s) in file
[webhook] 2022/01/23 17:46:45   loaded: simple-one
[webhook] 2022/01/23 17:46:45 serving hooks on http://0.0.0.0:9000/hooks/{id}
[webhook] 2022/01/23 17:46:53 [c02755] incoming HTTP POST request from 140.82.XX.XX:23757
[webhook] 2022/01/23 17:46:53 [c02755] simple-one got matched
[webhook] 2022/01/23 17:46:53 [c02755] simple-one hook triggered successfully
[webhook] 2022/01/23 17:46:53 [c02755] executing /root/tandysun/webhook/webhook-linux-amd64/echoparam (/root/tandysun/webhook/webhook-linux-amd64/echoparam) with arguments ["/root/tandysun/webhook/webhook-linux-amd64/echoparam" "refs/heads/master" "{\"after\":\"34008ad043f4aabd1333b6d65f730099145fbda1\",\"url\":\"https://api.github.com/users/jaydensun\"}}" "{\"Accept\":\"*/*\",\"X-Hub-Signature-256\":\"sha256=36bb1a0299df3f0d9f91abc98c83caa9866ced5ba44f8d3a0f10da3fe2fe2784\"}" "{}"] and environment [] using  as cwd
[webhook] 2022/01/23 17:46:53 [c02755] command output: refs/heads/master {"after":"34008ad043f4aabd1333b6d65f730099145fbda1","url":"https://api.github.com/users/jaydensun"}} {"Accept":"*/*",,"X-Hub-Signature-256":"sha256=36bb1a0299df3f0d9f91abc98c83caa9866ced5ba44f8d3a0f10da3fe2fe2784"} {}

[webhook] 2022/01/23 17:46:53 [c02755] finished handling simple-one
[webhook] 2022/01/23 17:46:53 [c02755] 200 | 8.2 KiB | 7.36354ms | 106.52.79.188:9000 | POST /hooks/simple-one
```

