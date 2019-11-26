
## 在DAP中使用
**

### 上传到DAP
将远程打好的包`view.zip` 上传到对应业务中心下的前端引擎模板
![](https://tva1.sinaimg.cn/large/006y8mN6gy1g9bfh8wd4kj31kc0u0wu8.jpg)

> 注意事项:
> 1. 上传前需确认此业务中心是否曾经上传过，若有，则只能更新，若没有，则需要新增
> 2. 若该业务中心没有上传过引擎模板，则默认使用公共业务中心的

### 在业务中心的功能配置中，新增功能
在业务中心中配置新的功能菜单，菜单的路径应该为在`dap-cli.json`中配置的`devRoutes`中的`path`值
![](https://tva1.sinaimg.cn/large/006y8mN6gy1g9bg1nqtg4j31k80u07fk.jpg)