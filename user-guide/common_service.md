## 通用服务
***
脚手架提供2个公共服务`网络请求服务`和`权限服务`，全局可以引用。

### 网络请求服务`NetworkService`
此服务提供了集成单点登录的网络支持

#### API

|方法名|参数|返回值|描述|
|-|-|-|:-|
|get|(`url`: string)|`Observable<any>`| get方法调用的网络请求 |
|post|(`url`: string, `params`: any)|`Observable<any>`| post方法调用的网络请求 |

#### 其他
?> 复杂请求，直接使用Angular7的`HttpClient`也能默认带上`TOKEN`验证机制

### 权限服务`DdmpAuthService`
|方法名|参数|返回值|描述|
|-|-|-|:-|
|get currentUser |  |`DdmpUser`| 获取当前用户信息，get类型的方法，可以直接`service.currentUser`调用 |
|get currentToken |  |`DdmpToken`| 获取当前token信息，get类型的方法，可以直接`service.currentToken`调用 |
|get currentUserObservable |  |`Observable<DdmpUser>`| 获取当前用户的`Observable`，用户改变时会通知，get类型的方法，可以直接`service.currentUserObservable`调用 |
|get currentTokenObservable |  |`Observable<DdmpToken>`| 获取当前token信息的`Observable`，token改变时会通知，get类型的方法，可以直接`service.currentTokenObservable`调用 |

```TS
export class DdmpUser {
    id?: any ;
    authCode: string ;
    code?: string ;
    name?: string ;
    sex?: string;
    headIcon?: string ;
    extraInfo?: any ;
    expireTime?: number ;
}
```
```TS
export class DdmpToken {
    authCode: string ;
    userCode?: string ; // 用户 code
    accessToken: string ; // 此处的 accessToken 信息
    refreshToken?: string ; // 此处为 refreshToken 信息
    authType?: string ; // 此处为认证类型
    accessExpireTime?:  number ; // 过期时间，毫秒数
}
```
