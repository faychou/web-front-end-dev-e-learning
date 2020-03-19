```
Docker 让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中,然后发布到任何服务器上去,容器之间都是沙盒状态

- Docker的理解: Docker就像一个虚拟机,在每一个容器中有这个项目的依赖,这就避免了相同的代码在不同的环境中出现的问题

- Docker核心的命令:docker build 通过配置文件构建自己的景象 docker pull 下载景象到本地 docker run 通过景象启动容器

- 查看当前启动的docker容器 docker container ls docker container ls -a 查看全部

- 删除容器 docker container rm (-f 强行删除) 容器ID

- 查看当前的景象 docker image ls
```