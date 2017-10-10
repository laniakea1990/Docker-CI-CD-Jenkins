Docker提供了开放的中央仓库dockerhub，同时也允许我们使用registry搭建本地私有仓库。搭建私有仓库有如下的优点：

* 节省网络带宽，提升Docker部署速度，不用每个镜像从DockerHub上去下载，只需从私有仓库下载就可；
* 私有镜像，包含公司敏感信息，不方便公开对外，只在公司内部使用。

## 搭建私有仓库

#### 下载 registry 镜像

`$docker pull registry`

#### 启动容器

`docker run -d --name registry-jrr -p 5000:5000 -v /opt/data/registry:/tmp/registry registry`

