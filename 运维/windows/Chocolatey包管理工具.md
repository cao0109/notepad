# Chocolatey包管理工具

## 1. 安装

1. 打开PowerShell，输入以下命令：

    ```shell
    Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
   > 注意：如果出现`Set-ExecutionPolicy`的错误，需要使用管理员权限打开PowerShell。

2. 检查安装是否成功

    ```shell
    choco -v
    ```
   ![](./assets/Chocolatey包管理工具-1679977280199.png)

3. 更新

    ```shell
    choco upgrade chocolatey
    ```

## 2. 常用指令

* choco list -li 查看本地安装的软件
* choco search nodejs 查找安装包
* choco install sublimetext3 下载
* choco uninstall sublimetext3 卸载
* choco upgrade sublimetext3 更新（update）

> 安装包的名称可以在[Chocolatey官网](https://chocolatey.org/packages)上查找。

## 3. 修改安装路径

1. 打开PowerShell，输入以下命令：

    ```shell
    choco feature enable -n=useRememberedArgumentsForUpgrades
    ```
2. 修改安装包的安装路径

    ```shell
    choco install sublimetext3 --params="'/InstallDir:D:\software\sublime_text_3'"
    ```

## 4. 修改安装源

1. 打开PowerShell，输入以下命令：

    ```shell
    choco sources add -n=chocolatey -s="https://mirrors.tuna.tsinghua.edu.cn/chocolatey/" --priority=1
    ```
2. 查看安装源

    ```shell
    choco sources list
    ```
3. 删除安装源

    ```shell
    choco sources remove -n=chocolatey
    ```
4. 更新安装源

    ```shell
    choco sources update -n=chocolatey -s="https://mirrors.tuna.tsinghua.edu.cn/chocolatey/"
    ```
5. 清除缓存

    ```shell
    choco cache clean
    ```
   

