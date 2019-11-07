## 配置文件详解
***

### 配置文件`.dap-cli.json`
dap的插件工程有特殊的配置描述文件`.dap-cli.json`，来支持插件的相关配置和`dap-cli-service`的运行

#### 文件结构
|字段名|类型|默认值|描述|
|-|-|-|:-|
|`version`|`字符串`|`0.0.1`| `.dap-cli.json`文件的版本，一般来说不变 |
|`name`|`字符串`|| 使用`dap-cli-service`脚手架生成时，生成的插件名称，**不允许改变** |
|`package`|`字符串`|| 使用`dap-cli-service`脚手架生成时，生成的`code`和`assets`路径，**不允许改变** |
|`dist`|`对象`|| 使用`云打包`之后，生成的dist文件下载的目录和文件名信息 |
|`dist`.`outputPath`|`字符串`|`./dist/`| 使用`云打包`之后，生成的dist文件下载的目录 |
|`dist`.`outputName`|`字符串`|`view.zip`| 使用`云打包`之后，生成的文件名, 根据`DAP`要求，需要以`.zip`结束 |
|`deps`|`数组`|`[]`| 插件包如果有新增`npm`依赖，需要在此处定义 |
|`dep.name`|`字符串`|| 依赖包的名字 |
|`dep.version`|`字符串`|| 依赖包的版本 |
|`dep.env`|`字符串`|| 依赖包是`dependencies`还是`devDependencies`, 如果不清楚，可以写`dependencies`,如果没有用到，不会影响打包体积 |
|`devRoutes`|`数组`|| 文件定义的路由，开发和编译时都需要，如果需要能显示，必须在此定义，使用懒加载的方式加载 |
|`devRoute.path`|`字符串`|| 路由的路径，在`DAP`上配置自定义页面时，需要定义此配置 |
|`devRoute.loadChildren`|`字符串`|| 路由模块的位置，懒加载的方式，必须是`src`开始的路径 |
|`devRoute.data.name`|`字符串`|| 开发时，需要显示在右侧菜单的名字 |
|`devEnv`|`对象`|| 开发时，相关的环境变量信息，主要来定义登录方式和网络请求接口 |
| `devEnv.needSSO` | `布尔值`| `false` | 是否采用`SSO`的方式进行单点登录 |
| `devEnv.baseDomain` | `字符串`|  | 权限获取接口的`URL`，一般为`DAP`配置应用的后端地址 |
| `devEnv.ssoDomain` | `字符串`|  | 单点登录的`URL`，只有当`needSSO`为`true`时生效 |
| `devEnv.clientId` | `字符串`|  | 单点登录的`clientId`，由单点系统分发 |
| `devEnv.appInfo` | `对象`|  | 单点登录系统的其他配置信息 |
| `devEnv.appInfo.appId` | `字符串` | | 单点登录系统的`appId` |
| `devEnv.appInfo.appSecre` | `字符串`|  | 单点登录系统的`appSecre` |
| `devEnv.appInfo.appCode` | `字符串`|  | 单点登录系统的`appCode` |
| `devEnv.appInfo.title` | `字符串`|  | 开发时，右上角显示标题 |
| `devEnv.appInfo.sidebarEnable` | `布尔值`|  | 不建议修改 |




#### 文件结构-示例
`json
{
  "version": "0.0.1",
  "name": "hello",
  "package": {
    "src": {
      "code": "./hello/",
      "assets": "./hello/"
    }
  },
  "dist": {
    "outputPath": "./dist/",
    "outputName": "view.zip"
  },
  "deps": [{
    "name": "zip-local",
    "version": "^0.3.4",
    "env": "dependencies"
  }, {
    "name": "https",
    "version": "^1.0.0",
    "env": "devDependencies"
  }],
  "devRoutes": [
    {
      "path": "app-plugin-plan-demo",
      "loadChildren": "src/plugins/hello/plan-demo/plan-demo.module#PlanSetModule",
      "data": {
        "name": "计划Demo"
      }
    }
  ],
  "devEnv": {
    "needSSO": false,
    "baseDomain": "http://k8s.definesys.com:30557",
    "ssoDomain": "http://k8s.definesys.com:30008",
    "appInfo": {
      "appId" : "fad5aa1e-335e-45d5-8ade-ba02f02d0c94",
      "appSecre" : "e13f61fe-6b3a-4fd1-8ff3-89b8b8e8fbc9",
      "appCode" : "plan-demo",
      "sidebarEnable": false,
      "title": "DAP脚手架"
    },
    "clientId": "dapAppClient"
  }
}
`
