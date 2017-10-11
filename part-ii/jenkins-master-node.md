## Jenkins master节点部署

#### 获取Jenkins镜像

从官方DockerHub上拉取Jenkins镜像：

```
root@ubuntu:/# docker pull qinyu/jenkins
Using default tag: latest
latest: Pulling from qinyu/jenkins
cfc728c1c558: Pull complete 
5b12b87f0a0e: Pull complete 
b7d6497a92f9: Downloading [========>                                          ]  12.24MB/70MB
7c6644ff6c63: Downloading [================>                                  ]   8.09MB/25.25MB
48973060083e: Download complete 
af01d2ab7c3e: Download complete 
aebc5599e186: Download complete 
e38dd7110c4f: Download complete 
141c7a122953: Downloading [=>                                                 ]  2.135MB/72.74M
```

#### **镜像使用方法**

* 设置管理员帐号密码（下面例子用户名和密码都是`admin`，可以自己调整）

```
root@ubuntu:/# echo "admin" | docker secret create jenkins-user -
vtukek9s2qqhaaief1gd9ybc2

root@ubuntu:/# echo "admin" | docker secret create jenkins-pass -
mhufs3gua7yt0th8fgm5xlqli
```

* 使用docker stack部署服务（配置见jenkins.yml，如有必要可以修改 volume /var/jenkins\_home的挂载路径）



