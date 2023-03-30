# docker

## 安装

### 安装docker

```bash
# 安装依赖
yum install -y yum-utils device-mapper-persistent-data lvm2

# 添加源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 安装dockerMarkdown Preview Enhanced: Image Helper
yum install docker-ce -y

# 启动docker
systemctl start docker

# 设置开机启动
systemctl enable docker

# 查看版本
docker version
```

### 安装docker-compose

```bash
# 下载
curl -L https://get.daocloud.io/docker/compose/releases/download/1.23.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# 添加执行权限
chmod +x /usr/local/bin/docker-compose

# 查看版本
docker-compose version
```
