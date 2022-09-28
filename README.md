每天打卡真烦，弄个自动打卡YYDS！！！

sysu打卡地址：https://cas.sysu.edu.cn/cas/login?service=https://portal.sysu.edu.cn/shiro-cas

因为打卡填写页面被改了一下，需要勾一下复选框，所以我稍微修改了下，改成v2.0版本

# 安装docker

看教程：https://yeasy.gitbook.io/docker_practice/install

# 拉取打卡镜像

1、镜像放在私有云里面，没有配置https（懒得配），所以拉取的时候要设置下docker守护进程，需要修改下/etc/docker/daemon.json，这是ubuntu的docker配置，centos和其他发行版类似。如果没有这个文件就创建。

```
sudo vim /etc/docker/daemon.json
```

2、如果是第一次创建这个文件的，添加如下内容到/etc/docker/daemon.json

```
{"insecure-registries":["106.52.218.192:5353"]}
```

如果之前就有这个文件了，则添加下面这个到{}里面，注意下格式，可能需要逗号

```
"insecure-registries":["106.52.218.192:5353"]
```

3、重启docker守护进程和docker

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

4、拉取镜像，比较大有1G多，耐心等等喝杯茶........

```
sudo docker pull 106.52.218.192:5353/sysu/auto_daka:v2.0
```



# 启动

## 默认6点10分打卡

为什么是6点10分呢？因为没那么容易没发现！

设置下面的USER_NAME、PASSWORD参数（-e），就是自己的NETID的账号密码，这个一定要设不然不知给谁打卡

```
sudo docker run -dit --restart=always --name sysu_daka_test -v /etc/localtime:/etc/localtime -e USER_NAME="xxx" -e PASSWORD="xxx" -e UNSTOPPABLE="false" -w /code/jksb_sysu 106.52.218.192:5353/sysu/auto_daka:v2.0 python /code/jksb_sysu/jksb_sysu.py
```



## 自定义打卡时间

设置下面的USER_NAME、PASSWORD参数（-e）的同时，设置HOUR和MINUTE参数，那么打卡时间就是每天的HOUR时：MINUTE分，比如下面设成每天8点10分打卡

```
sudo docker run -dit --restart=always --name sysu_daka_test -v /etc/localtime:/etc/localtime -e USER_NAME="xxx" -e PASSWORD="xxx" -e HOUR="8" -e MINUTE="10" -e UNSTOPPABLE="false" -w /code/jksb_sysu 106.52.218.192:5353/sysu/auto_daka:v2.0 python /code/jksb_sysu/jksb_sysu.py
```



## 无脑鬼畜疯狂打卡

设置下USER_NAME、PASSWORD参数（-e），跟定时打卡区别就是UNSTOPPABLE参数为true，疯狂无脑不停打卡，辅导员来找你别来找我！这个主要是用来测试打卡是否有效，/*慎用*/

```
sudo docker run -dit --restart=always --name sysu_daka_test -v /etc/localtime:/etc/localtime -e USER_NAME="xxx" -e PASSWORD="xxx" -e UNSTOPPABLE="true" -w /code/jksb_sysu 106.52.218.192:5353/sysu/auto_daka:v2.0 python /code/jksb_sysu/jksb_sysu.py
```



# 注意

1、USER_NAME、PASSWORD参数（-e），这两个参数一定要设置！！！不然不知道给谁打卡！

2、打卡可能会有延迟，会比设定的时间晚大概半分钟，企业微信才收到打卡成功通知
