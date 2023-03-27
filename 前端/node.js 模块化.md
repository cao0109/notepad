# node.js 模块化

## 1. 优点

① 复用性

②可维护性

③按需加载

## 2. 规范

### 2.1 分类

1.内置模块（官网提供，fs/http）

2.自定义模块（自己写的js文件）

3.第三方模块

### 2.2 模块加载

> require 
>
> 加载模块时，会自动运行被加载模块中的程序
>
> 注意：.js后缀可省略

```js
//1.加载内置模块
const http = require('http')

//2.加载自定义模块
const fs = require('./fs_wr.js')
//require得到自定义模块的module.exports

//3.加载第三方模块
const fs = require('mongoose')
```

### 2.3 模块作用域

#### 1.定义

类似函数作用域

默认情况下在自定义模块中定义的变量，方法等成员，只能在当前模块内访问。

这种模块级别的访问限制，加模块作用域

#### 2. 优点

防止全局变量污染

### 2.4 向外共享模块作用域中成员

.js自定义模块中的module对象，储存了和当前模块有关的信息

![1655805658587](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655805658587.png)

> 在一自定义模块中，默认情况下，module.exports={} 
>
> 使用require方法导入模块时，导入结果以module.exports指向的对象为准

#### 1.exports对象

默认情况下与module.exports指向同一个对象，

**但最终共享结果，以module.exports指向对象为准**

![1655863087192](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655863087192.png)

![1655863152408](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655863152408.png)

![1655863400080](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655863400080.png)

> 结论：exports，module.exports别一起用就行

## 3. 模块加载机制

模块在第一次加载后会被缓存。 

意味着多次调用 require() 不会导致模块的代码被执行多次。从而提高模块的加载效率。

### 3.1 加载优先级

内置模块的加载优先级最高

### 3.2 自定义模块的加载机制

使用 require() 加载自定义模块时，必须指定以 **./** 或 **../** 开头的路径标识符。在加载自定义模块时，如果没有指定 ./ 或 ../ 这样的路径标识符，则 node 会把它当作内置模块或第三方模块进行加载。

同时，在使用 require() 导入自定义模块时，如果省略了文件的扩展名，Node.js 会按顺序分别尝试加载以下的文件：

- 按照确切的文件名进行加载
- 补全 .js 扩展名进行加载
- 补全 .json 扩展名进行加载
- 补全 .node 扩展名进行加载
- 加载失败，终端报错

### 3.3 第三方模块的加载机制

​	如果传递给 require() 的模块标识符不是一个内置模块，也没有以 ‘./’ 或 ‘../’ 开头，则 Node.js 会从当前模块的父目录开始，尝试从 /node_modules 文件夹中加载第三方模块。 
​	如果没有找到对应的第三方模块，则移动到再上一层父目录中，进行加载，直到文件系统的根目录。
​	例如，假设在 'C:\Users\itheima\project\foo.js' 文件里调用require('tools')，则 Node.js 会按以下顺序查找：

-  **C:\Users\itheima\project\node_modules**\tools
-  **C:\Users\itheima\node_modules**\tools
-  **C:\Users\node_modules**\tools
-  **C:\node_modules**\tools



### 3.4 目录作为模块

当把目录作为模块标识符，传递给 require() 进行加载的时候，有三种加载方式：

- 在被加载的目录下查找一个叫做 package.json 的文件，并寻找 main 属性，作为 require() 加载的入口
- 如果目录里没有 package.json 文件，或者 main 入口不存在或无法解析，则 Node.js 将会试图加载目录下的 index.js 文件。
- 如果以上两步都失败了，则 Node.js 会在终端打印错误消息，报告模块的缺失：Error: Cannot find module 'xxx'

# npm与包

## 1. 包

第三方模块==包

>   https://www.npmjs.com/ 网站上搜索所需要的包
>   https://registry.npmjs.org/  服务器上下载需要的包

npm 包管理工具 **Node Package Manager**

> 格式化时间的包 moment

## 2. 安装包

> npm install 包名
>
> npm i 包名
>
> 默认安装最新版本
>
> npm i 包名@版本号     安装指定版本

初次安装包后，项目文件夹下出现：

- node_modules 的文件夹
- package-lock.json 的配置文件

**node_modules** 文件夹用来存放所有已安装到项目中的包。require() 导入第三

方包时，就是从这个目录中查找并加载包。

**package-lock.json** 配置文件用来记录 node_modules 目录下的每一个包的下载

信息，例如包的名字、版本号、下载地址等。

## 3. 包管理配置文件

### 3.1 快速创建 package.json

> npm init -y
>
> 只能在英文的目录下成功运行！不能出现空格。
> 运行 npm install 命令安装包时，npm 包管理工具会自动把包的名称和版本号，记录到 package.json 中。

### 3.2 dependencies 节点

![1655866120133](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655866120133.png)

记录使用 npm install 命令安装了哪些包。

### 3.3 一键安装所有包

> npm i

### 3.4 卸载

> npm uninstall 包名

npm uninstall 命令执行成功后，会把卸载的包，自动从 package.json 的 dependencies 中移除掉。

### 3.5 devDependencies 节点

> npm i 包名 -D
>
> npm install 包名 --save-dev

如果某些包只在项目开发阶段会用到，在项目上线之后不会用到，则建议把这些包记录到 devDependencies 节点中。

与之对应的，如果某些包在开发和项目上线之后都需要用到，则建议把这些包记录到 dependencies 节点中。

### 3.6 npm 镜像源

![1655866994870](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655866994870.png)

### 3.7 nrm

更方便的切换下包的镜像源，利用 nrm 提供的终端命令，可以快速查看和切换下包的镜像源。

![1655867107036](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655867107036.png)

> nrm : 无法加载文件 D:\Softstore\nodejs\node_global\nrm.ps1，因为在此系统上禁止运行脚本。
>
> 解决方案： [powershell](https://so.csdn.net/so/search?q=powershell&spm=1001.2101.3001.7020)  执行 **set-ExecutionPolicy RemoteSigned** 



## 4. 包的分类

- 项目包
- 全局包

### 4.1 项目包

安装到项目的 node_modules 目录中的包，都是项目包。

项目包又分为两类，分别是：

- **开发依赖包**（被记录到 devDependencies 中的包，只在开发期间会用到）
- **核心依赖包**（被记录到 dependencies 节点中的包，在开发期间和项目上线之后都会用到）

### 4.2.全局包

在执行 npm install 命令时，如果提供了 **-g** 参数，则会把包安装为全局包。

安装在 **C:\Users\用户目录\AppData\Roaming\npm\node_modules** 目录

工具性质的包，才有全局安装的必要性。

### 4.3. i5ting_toc

i5ting_toc 是一个可以把 md 文档转为 html 页面的小工具，使用步骤如下：

![1655867905620](node.js%20%E6%A8%A1%E5%9D%97%E5%8C%96.assets/1655867905620.png)



## 5. 包结构的规范

一个规范的包，它的组成结构，必须符合以下 3 点要求：

- 包必须以单独的目录而存在

- 包的顶级目录下要必须包含 package.json 这个包管理配置文件

- package.json 中必须包含 name，version，main 这三个属性，分别代表包的名字、版本号、包的入口。

  

  关于更多的约束，可以参考如下网址：
  https://yarnpkg.com/zh-Hans/docs/package-json