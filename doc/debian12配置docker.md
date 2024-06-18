# Debian12安装Docker并配置加速

## 安装

使用docker的一键安装脚本（```curl -fsSL https://get.docker.com | sh```）失败后，只能手动安装docker，做如下记录。

### 更新系统软件包

安装一些必须得软件包：

```shell

apt update
apt upgrade -y
apt install apt-transport-https ca-certificates
```

### 移除存在的Docker

```shell

sudo apt-get remove docker docker-engine docker.io containerd runc
```

### 配置Docker的GPG公钥和apt源

添加 Docker 官方GPG密钥并将 Docker 存储库添加到软件包源列表中：

```shell

curl -sSL https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://download.docker.com/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list
```

也可以使用如下命令：

```shell

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
```

国内机器可以用清华源：

```shell

curl -sS https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://mirrors.tuna.
```

### 安装Docker

配置完后可以安装**Docker CE**了：

```shell

apt update
apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

安装完后可以用```docker version```查看是否安装成功。

#### 简单运行docker

##### 验证

可以使用以下命令检查 Docker 是否安装成功并是否在系统启动时自动启动：

```shell

systemctl is-active docker
```

##### 测试

可以通过启动 hello-world Docker镜像来测试安装是否成功：

```shell

docker run hello-world
```

### 配置Docker rootless 模式

将某个特定用户加入 docker 组：

```shell

apt install docker-ce-rootless-extras
```

### 配置Docker 用户组

默认情况下，Docker 需要 root 权限才能运行。为了避免每次使用 Docker 时都需要输入 sudo，您可以将您的用户添加到 docker 组中。运行以下命令来完成此操作：

```shell

sudo usermod -aG docker $USER
```

### 安装docker compose

因为我们已经安装了 docker-compose-plugin，所以 Docker 目前已经自带 docker compose 命令，基本上可以替代 docker-compose。

如果某些镜像或命令不兼容，则单独安装 Docker Compose，使用 Docker 官方发布的 Github 直接安装最新版本：

```shell

curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-Linux-x86_64 > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```

安装后使用 docker-compose version 命令检查是否安装成功。

## 配置Docker

### 配置镜像加速

通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

[加速镜像地址](https://tvtv.fun/mirrors-list.html​tvtv.fun/mirrors-list.html)

```shell

sudo tee /etc/docker/daemon.json <<-'EOF'
{"registry-mirrors": ["https://hub-mirror.c.163.com"]}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
docker info
```

或者使用阿里云的专属镜像加速
[阿里云镜像获取地址](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)
登陆后，左侧菜单选中镜像加速器就可以看到你的专属地址了。

### sample

以下配置会增加一段自定义内网 IPv6 地址，开启容器的 IPv6 功能，以及限制日志文件大小:

```shell

cat > /etc/docker/daemon.json << EOF
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "20m",
        "max-file": "3"
    },
    "ipv6": true,
    "fixed-cidr-v6": "fd00:dead:beef:c0::/80",
    "experimental":true,
    "ip6tables":true
}
EOF
```

然后重启 Docker 服务：

```shell

systemctl restart docker
```

#### sample2

```shell

sudo mkdir -p /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
    "bip": "172.17.0.1/16",
    "registry-mirrors": [
        "https://registry.docker-cn.com",
        "http://hub-mirror.c.163.com"
    ],
    "exec-opts": [
        "native.cgroupdriver=systemd"
    ],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m"
    },
    "storage-driver": "overlay2"
}
EOF
```
