# docker学习使用

安装什么的就略过了，之前已经整理过，这里就说说自己使用中的一些东西，也是初用，记录下(现在使用win10 64位，
使用Docker for Windows 直接安装就好[需要专业版win10安装hyper-v，就不需要vbox了]，低版本(ps:win7/8)使用Docker Toolbox)

国内环境还是使用daocloud提供的[docker加速器按](https://www.daocloud.io/mirror)中的配置就好

创建一个要使用的容器

其实我想使用一个mongo容器（win7的vagrant中安装的docker），想要在win中访问mongo，一开始run一个，就是连不上，考虑是端口问题

之前只映射了win和vm的端口，而vm中的docker却没有映射到vm中，最终命令如下：

docker run --name mongo -d -p 27017:27017 mongo:latest

与其他容器建立连接需要添加--link参数，docker run --name xxx --link xxx ...

--name 容器名 --link 要连接的容器

-d表示让容器后台运行，-p表示端口映射（docker port:宿主机port），可以使用多个-p来映射多个端口，这里只介绍使用的参数，其他参数可以百度之

然后win中访问docker的mongo服务，success!（使用的mongovue连接）

开启一个容器

docker start [-i] containerName (添加-i参数会进入交互模式伪终端)

进入运行中的容器

docker exec -ti containerName /bin/bash (只有-i参数时，交互模式中没有如
[root@6b879f1a250b:/#]的终端标识；只有-t，获取不到stdin，看不到命令执行情况) exec方式退出时，容器会继续运行。

docker attach containerName (attach可以进入到一个已经运行的容器的stdin，然后执行“默认”命令；如果从这个stdin中exit，会导致容器的停止)

另外，用exec来执行容器中比较耗时命令时，添加-d参数，让它在后台运行，不会卡住终端界面（docker  exec -d containerName /opt/xxx.sh）