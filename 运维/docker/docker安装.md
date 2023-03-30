## Centos

### 安装

```bash
# 安装依赖
yum install -y yum-utils device-mapper-persistent-data lvm2

# 添加源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 安装
yum install docker-ce -y
```

### 启动

```bash
systemctl start docker
```

### 配置

```bash
# 设置开机启动
systemctl enable docker

# 设置镜像加速器
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
EOF
systemctl daemon-reload
systemctl restart docker

# 重启docker
systemctl restart docker

# 查看docker版本
docker version

# 查看docker信息
docker info
```

## Ubuntu

### 安装

```bash
# 安装依赖
apt-get update
apt-get install -y apt-transport-https ca-certificates curl software-properties-common

# 添加源
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"

# 安装
apt-get update
apt-get install docker-ce -y
```

### 启动

```bash
systemctl start docker
```

### 配置

```bash
# 设置开机启动
systemctl enable docker

# 设置镜像加速器
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
EOF
systemctl daemon-reload
systemctl restart docker

# 重启docker
systemctl restart docker

# 查看docker版本
docker version

# 查看docker信息
docker info
```

## Windows

### 安装

```bash
# 下载安装包
https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe
```

### 配置

```bash
# 设置开机启动
右键Docker图标 -> Settings -> General -> Start Docker when you log in -> Apply

# 设置镜像加速器
右键Docker图标 -> Settings -> Daemon -> Registry mirrors

# 查看docker版本
docker version
```

## Mac

### 安装

```bash
# 下载安装包
https://download.docker.com/mac/stable/Docker.dmg
```

### 配置

```bash
# 设置开机启动
Docker -> Preferences -> General -> Start Docker when you log in -> Apply

# 设置镜像加速器
Docker -> Preferences -> Daemon -> Registry mirrors

# 查看docker版本
docker version
```

