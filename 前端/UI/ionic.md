# Ionic(移动端UI框架)

## 1. ionic介绍

ionic 是一个强大的 HTML5 应用程序开发框架(HTML5 Hybrid Mobile App Framework )。 可以帮助您使用 Web 技术，比如 HTML、CSS 和
Javascript 构建接近原生体验的移动应用程序。

ionic 主要关注外观和体验，以及和你的应用程序的 UI 交互，特别适合用于基于 Hybird 模式的 HTML5 移动应用程序开发。

## 2. ionic的特点

* ionic 基于Angular语法，简单易学。
* ionic 是一个轻量级框架。
* ionic 完美的融合下一代移动框架，支持 Angularjs 的特性， MVC ，代码易维护。
* ionic 提供了漂亮的设计，通过 SASS 构建应用程序，它提供了很多 UI 组件来帮助开发者开发强大的应用。
* ionic 专注原生，让你看不出混合应用和原生的区别
* ionic 提供了强大的命令行工具。
* ionic 性能优越，运行速度快。

## 3. 开始一个ionic项目

### 3.1 安装ionic

```bash
npm i -g @ionic/cli
```

### 3.2 创建一个ionic项目

```bash
  ionic start

  Every great app needs a name! 😍

    Please enter the full name of your app. You can change this at any time.
    To bypass this prompt next time, supply name,
    the first argument to ionic start.

  ? Project name: █
```

或者

```bash
ionic start myApp tabs
```

`myApp` 为项目名称，`tabs` 为项目模板，ionic提供了多种模板，包括`tabs`、`sidemenu`、`blank`、`super`、`conference`等。
可以通过`ionic start --list`查看所有模板。

### 3.3 运行项目

```bash
ionic serve
or
ng serve --open
```

## 4. Ionic CLI 脚手架

### 4.1 项目结构

```bash
src/
├── app/
├── assets/
├── environments/
├── theme/
├── global.scss
├── index.html
├── main.ts
├── polyfills.ts
├── test.ts
└── zone-flags.ts
```

* `app/` 应用程序目录，包含应用程序的所有组件、服务、模块等。
* `assets/` 静态资源目录，包含应用程序的所有静态资源，如图片、字体等。
* `environments/` 环境配置目录，包含应用程序的所有环境配置。
* `theme/` 主题目录，包含应用程序的所有主题配置。
* `global.scss` 全局样式文件。
* `index.html` 应用程序入口文件。
* `main.ts` 应用程序主入口文件。
* `polyfills.ts` 应用程序兼容性文件。
* `test.ts` 应用程序测试文件。
* `zone-flags.ts` 应用程序区域标志文件。
* `ionic.config.json` ionic配置文件。

### 4.2 生成新要素

```bash
  ionic generate
? What would you like to generate?
❯ page
  component
  service
  module
  class
  directive
  guard
```

也可以使用`ionic g`简写命令。
可以控制生成的路径，如`ionic g page pages/home`。

```shell
  ionic generate
? What would you like to generate? page
? Name/path of page: portfolio █
```

### 4.3 ios 开发

可以使用`Capacitor`或者`Cordova`来开发ios应用。
[官方文档](https://ionicframework.com/docs/developing/ios)

> ios开发需要mac系统。
>
> ios开发需要安装xcode 或 Ionic CLI。

### 4.4 android 开发

可以使用`Capacitor`或者`Cordova`来开发android应用。
[官方文档](https://ionicframework.com/docs/developing/android)
