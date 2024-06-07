**目录**
- [Docker](#docker)
    - [Docker入门](#docker入门)
    - [必备概念](#必备概念)
    - [环境准备](#环境准备)
    - [系统初始化](#系统初始化)
    - [安装docker](#安装docker)
    - [案例1：部署Flask网站](#案例1部署flask网站)
- [Dockerfile指令详解](#dockerfile指令详解)
    - [Dockerfile指令](#dockerfile指令)
    - [构建](#构建)
    - [创建容器](#创建容器)
    - [容器的销毁](#容器的销毁)
    - [常见操作](#常见操作)
    - [案例2：Django项目部署](#案例2django项目部署)
- [Docker创建的ubuntu配置](#docker创建的ubuntu配置)
    - [创建容器后安装](#创建容器后安装)
    - [将主机文件传到docker容器](#将主机文件传到docker容器)
    - [将docker容器文件传到主机](#将docker容器文件传到主机)
    - [将容器生成镜像](#将容器生成镜像)
    - [给容器指定ip](#给容器指定ip)
    - [安装nginx](#安装nginx)
    - [安装uwsgi](#安装uwsgi)
## Docker
#### Docker入门
1. docker是什么？
   - docker就是一个**软件**，支持在win、mac、linux系统上安装
   - 可以在一台电脑上根据**模板**创建出多个**隔离的环境**，相比于其他方式而言极大的**节省资源**
2. 为什么要创建隔离的环境？
   - 假设你先在有一个centos系统上运行程序，而这个程序依赖**ubuntu系统的服务A** + **centos系统的服务B**
     1. 传统方式：买新服务器
     2. 虚拟化方式：在原来的牛逼服务器上利用虚拟化技术创建虚拟机（vmware、kvm）等，用虚拟机做隔离去跑新的业务
     3. docker方式：利用docker创建一个新的容器[^1]，用容器去做隔离区跑新的业务
[^1]:隔离的环境
3. 为什么docker可以比虚拟机极大的节省资源？
   - 虚拟机的方式，是在电脑上完整的创建一个操作系统
   - docker的方式，创建的容器不是一个完整的操作系统，而是充分利用**宿主机内核**+**进程**，只是创建了一些必备的资源
4. Tips
   - 镜像：模板，例如[centos+mysql]、[ubuntu+mysql]、[ubuntu+mysql+redis]（基础镜像 + 自定义镜像）
#### 必备概念
![](Django部署/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-01-18%20100051.png)
- DockerHub,[远程镜像仓库](https://hub.docker.com/)
    > 仓库中有官方的一些镜像文件，也有开发者自定义的镜像文件
#### 环境准备
- Linux：centos
- 网络配置
![](Django部署/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-01-18%20103111.png)
```shell
# 修改网络配置文件
cd /etc/sysconfig/network-scripts/
vim ifcfg-ens33
# 应用修改
service network restart
systemctl restart network
```
#### 系统初始化
- 关闭selinux
  - 查看状态：`getenforce`
  - 临时关闭：`serenforce 0`
  - 永久关闭
    ```
    vim /etc/selinux/config
    # 设置为disabled
    SELINUX=disabled
    ```
- 防火墙
  - 查看防火墙状态：`systemctl status firewalld`
  - 关闭：`systemctl stop firewalld`
  - 关闭开机自启防火墙：`systemctl disable firewalld`
- net-tools：可以查看电脑的网络情况
`yum install net-tools -y`
- openssh-server
```shell
yum install openssh-server -y
systemctl start sshd.service
systemctl enable sshd.service
# 之后可以在win等系统使用ssh等命令去连接linux
```
- wget
`yum install wget -y`
- centos常用工具包
```shell
yum install -y wget bash-completion vim lrzss wget expect net-tools nc nmap tree dos2unix htop iftop iotop unzip sl psmic nethogs glances bc ntpdate openldap-devel
```
- 修改host
  - 在windows本地C://Windows/System32/drivers/etc/hosts写入`ip name:`
#### 安装docker
1. 安装docker-ce社区版
   - 配置repo源
    ```shell
    curl -o /etc/yum.repos.d/Centos-7.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    curl -o /etc/yum.repos.d/docker-ce.repo http://mirrors.aliyun.com/docker-ce/centos/docker-ce.repo
    yum clean all && yum makecache
    ```
   - 查看可下载版本
    ```shell
    yum list docker-ce --showduplicates | sort -r
    ```
   - 安装
    ```shell
    # 最新版
    yum install -y docker-ce

    # 指定版本
    yum install -y docker-ce-23.0.6
    ```
2. 启动docker-ce社区版
   - 设置开机启动
    ```shell
    systemctl enable docker
    ```
   - 启动docker
    ```shell
    systemctl start docker
    systemctl restart docker
    ```
   - 停止docker
    ```shell
    systemctl stop docker
    ```
   - 其他
    ```shell
    # 查看docker信息
    docker version

    # 查看docker信息
    docker info

    # docker-client
    ```
3. 配置docker
```shell
# 查看本地镜像
docker images

# 搜索镜像
docker search xxx:版本号

# 拉取镜像
docker pull xxx:版本号

# 配置加速器
# 在下方两个网址注册得到加速器地址
# https://cr.consloe.aliyun.com/cn-hangzhou/instances/mirrors
# https://www.daocloud.io/mirros#accelerator-doc
# 配置加速器到docker
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<- 'EOF'
{
    "registry-mirrors":[
        # 在上面网址中注册后得到的加速器地址
    ]
}
EOF
systemctl daemon-reload
systemctl restart docker
```
4. 端口的转发
   - 从外部访问某些功能时需要做端口的转发以便内部可以访问相应的组件
    ```shell
    cat <<EOF > /etc/sysctl.d/docker.conf
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    net.ipv4.conf.default.rp_filter = 0
    net.ipv4.conf.all.rp_filter = 0
    net.ipv4.ip_forward = 1
    EOF

    sysctl -p /etc/sysctl.d/docker.conf
    ```
---
#### 案例1：部署Flask网站
- 需求：基于docker创建在ubuntu18.04系统上运行我们开发的Flask网站
- 流程
  1. 在linux服务器安装docker
  2. 基于docker软件做以下操作
     1. 获取基础镜像ubuntu18.04
     2. 在基础颈项上构建自定义镜像【ubuntu18.04+python+代码】
     3. 基于镜像创建容器 + 运行
- 步骤
  1. 获取镜像：`docker pull ubuntu:18.04`
  2. 查看镜像：`docker images`
  3. 构建镜像
     1. Dockerfile
        ```shell
        # Dockerfile

        # Base images 基础镜像
        FROM ubuntu:18.04

        # MAINTAINER 维护者信息
        MAINTAINER (name) xxxxxx@xxx.com

        # RUN 执行以下命令
        # 给ubuntu的apt换阿里源
        RUN sed -i 's@http://archive.ubuntu.com/ubuntu/@http://mirrors.aliyun.com/ubuntu/@g' /etc/apt/sources.list
        RUN apt update
        RUN apt install python3 python3-pip -y
        RUN pip3 install flask
        RUN mkdir -p /data/www/

        # 拷贝代码文件至工作目录
        COPY app.py /data/www/app.py

        # 工作目录
        WORKDIR /data/www/

        # EXPOSE 映射端口
        EXPOSE 80

        # 容器启动时执行命令
        CMD ["python3", "app.py"]
        ```
        - 在docker宿主机centos7.9 **创建** 2个文件：
          - Dockerfile
          - app.py
        - 命令构建自定义镜像
            `docker build -t(tag) xxx(name):xxx(version) .(current dir) -f Dockerfile`
     2. 基于镜像创建容器 + 运行
        `docker run xxx(docker name):xxx(version)`
        - 做端口转发：宿主机的80端口转发的到程序的8000端口
        `docker run -p 80:8000 (--name xxx) xxx:xxx`
        - Tips
          - 如果想要不占用终端窗口可以这样做：
            ```shell
            # 这个指令会返回容器ID
            docker run -d -p 80:8000 xxx:xxx
            ```
          - 想要停止容器的运行可以这样做：
            ```shell
            # 查看当前正在存活的容器
            docker ps

            # 查看所有的容器
            docker ps -a

            # 停止正在存活的容器
            docker stop (name or 容器id前三位)

            # 删除一些已经停止的容器
            docker rm (name or 容器id前三位)
            ```
---
## Dockerfile指令详解
#### Dockerfile指令
- 创建镜像时运行
![](Django部署/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-01-18%20154742.png)
ENV是设置环境变量
![](Django部署/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-01-18%20155112.png)
![](Django部署/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-01-18%20155312.png)
- 容器运行
```shell
# 多个容器运行指令没用，只会运行最后一条
CMD ["python3", "app.py"]
CMD ["python3", "app.py"]
...
# CMD中的指令可以在docker run中覆盖掉
# 例如docker run -d -p 80:8000 xxx:xxx python2 app.py
# ENTRYPOINT指令不会被覆盖掉，但执行run时会将CMD覆盖的位置的内容当作参数
ENTRYPOINT ["python3", "app.py"]
ENTRYPOINT ["python3", "app.py"]
...
```
- Tips
  - 可以编写一个shell脚本，用于执行CMD或者ENTRYPOINT指令
#### 构建
```shell
# 这种方式会在第一次构建时将下载的数据缓存下来，之后再构建的话就会使用缓存中的文件，而不是再网络上重新下载
docker build -t(tag) xxx(name):xxx(version) .(current dir) -f Dockerfile

# 想要去除缓存，可以这样做
docker build -t(tag) xxx(name):xxx(version) .(current dir) -f Dockerfile --no-cache
```
#### 创建容器
1. 容器中必须要有一个前台进程，否则的话，创建之后立即销毁
2. 宿主机在运行时是否需要前台？不需要，一般以后台的进程来运行
3. 进入系统，人为的保持前台进程，以防止容器销毁
```shell
docker run -ti ubuntu:18.04 bash
docker run -ti ubuntu:18.04 sh
```
4. 添加前台进程(一般不做这个操作，一般都是后台执行)
```shell
docker run -ti ubuntu:18.04 ping baidu.com
```
#### 容器的销毁
```shell
# 单个删除
docker stop 容器ID
docker rm 容器ID

# 批量删除
docker stop `docker ps -aq`
docker rm `docker ps -aq`
```
#### 常见操作
1. 镜像含前台进程，创建容器 + 启动
`docker run -d -p 80:8000 --name xxx xxx:xxx`
2. 镜像不含前台进程，进入系统
`docker run -ti ubuntu:18.04 bash`
`docker run -ti ubuntu:18.04 sh`
3. 再次进入正在存活的镜像
`docker exec -ti 容器ID bash`

---
#### 案例2：Django项目部署
- 需求：在容器中创建centos7.9系统 + Python3.9.5 + django3.2 + gitee拉代码
- 流程：
  1. 下载基础镜像centos7.9
  2. 编写Dockerfile -> Python3.9.5 + django3.2 + gitee拉代码 + 前台运行django
  3. 构建镜像
  4. 创建容器 + 运行
- 步骤
  1. 下载基础镜像
     - `docker pull centos7.9.2009`
  2. 编写Dockerfile
  3. 启动docker
---
## Docker创建的ubuntu配置
#### 创建容器后安装
```shell
# 修改 apt源
sed -i 's@http://archive.ubuntu.com/ubuntu/@http://mirrors.aliyun.com/ubuntu/@g' /etc/apt/sources.list

apt update
apt install -y curl wget vim  # 安装常见软件
apt install python3.9  
apt install -y python3-distutils

curl 'https://bootstrap.pypa.io/get-pip.py' > get-pip.py   #  下载 pip
python3.9 get-pip.py  # 安装pip

ln -s /usr/bin/python3.9 /usr/bin/python  # 建立软连接

# 验证安装
python --version
pip --version
```
#### 将主机文件传到docker容器
```shell
docker cp /path/on/host container_id:/path/in/container
```
#### 将docker容器文件传到主机
```shell
docker cp container_id:/path/in/container /path/on/host
```
#### 将容器生成镜像
```shell
docker commit <container-id> ubuntu:20.04_py39
```
#### 给容器指定ip
1. 创建网络模型
```shell
# docker network create: 创建一个 Docker 网络的命令。
# --subnet=192.168.0.0/16: 指定网络的子网。在这里，子网为 192.168.0.0/16，表示该网络可以包含 192.168.0.0 到 192.168.255.255 范围内的 IP 地址。
# my-net: 指定创建的网络的名称为 my-net。你可以替换为任何你喜欢的名称。
docker network create --subnet=192.168.0.0/16 my-net
```
2. 指定ip
```shell
docker run -it --network my-net --ip 192.168.0.2 my-ubuntu-img

# 还可以指定mac地址
docker run -it --mac-address xx:xx:xx:xx:xx:xx --network my-net --ip 192.168.0.2 my-ubuntu-img
```
#### 安装nginx
```shell
apt install nginx

# 安装依赖
apt install curl gnupg2 ca-certificates lsb-release
```
#### 安装uwsgi
```shell
apt install gcc

# 安装uwsgi构建依赖
apt install build-essential python3-dev

pip install uwsgi
```