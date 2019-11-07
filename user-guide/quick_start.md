## 快速开始
***

### 创建插件项目
> 执行以下命令, dap-cli-service会在当前目录下新建一个名称为 `dap-plugin-scaffold-hello` 的文件夹

```shell
npx dap-cli-service init hello
```

#### 执行结果
![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8om2ggbkjj30j007waaq.jpg)

### 安装依赖
```shell
cd dap-plugin-scaffold-hello
npm install
```

### 开发调试
一键启动调试，运行成功后显示欢迎页面。

```shell
npm run start
```

#### 浏览器查看
浏览器输入 [http://localhost:4800](http://localhost:4800)
![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8ombdnz4dj31gw0sp409.jpg)

### 脚手架工具
!> 如果开发时网络条件不好，可以全局安装此`cli`命令行, 使用时不加`npx`前缀即可

```shell
npm install -g dap-cli-service
```
