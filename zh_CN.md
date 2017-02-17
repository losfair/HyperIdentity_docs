# HyperIdentity SSO 开发文档

### 常量

HyperIdentity 公共服务域: `hyperidentity.ifxor.com` （仅支持 HTTPS ）。以下所有调用地址均位于此域下。

### OpenID

HyperIdentity 平台目前提供使用简单、易于集成的 OpenID 服务。

调用方法: 

首先，发起方跳转页面到 `/web/?callback=CALLBACK_URL#auth`
（其中 `CALLBACK_URL` 为认证成功后的回调地址，不可带有任何 `?` 或 `#` 参数，需经过 URLEncode 编码，且其域名部分必须与来源 (Referrer) 域相同。
301 / 302 跳转不会包含 `Referer` 头，所以请使用客户端 JavaScript 或链接进行跳转。）

`CALLBACK_URL` 接收到通过 GET 方式传递的回调参数 `client_token` 后，向 `/identity/verify/verify_client_token` 发起 POST 请求
( body: `client_token=CLIENT_TOKEN`, `CLIENT_TOKEN` 为接收到的 `client_token` 回调参数 ) 并解析返回的 JSON 格式数据。

正常情况下的返回数据示例:

    {
      "err": 0,
      "msg": "OK",
      "userId": "838ba06d-d31b-4db2-8174-4ebf88388605",
      "username": "testuser",
      "domain": "demo.example.com"
    }

其中 `userId` 为用户唯一 ID ，`username` 为用户名。

如果 `err` 不为 0 ，说明认证失败。失败原因包含在 `msg` 中。

### OAuth

HyperIdentity 平台目前提供试验性的 OAuth 服务。

调用方法：

首先，开发者需要登录自己的 HyperIdentity 账户并创建服务，保存好 `serviceId` 和 `secretKey`。其中 `serviceId` 可以公开，`secretKey` 必须保密。

在开发者的服务能够使用 OAuth 提供的用户相关接口前，用户需要在自己的 HyperIdentity 个人中心内授权相应服务。`serviceId` 即为服务授权所需的标识符，开发者引导用户输入即可。

接口参数和返回内容传递均使用 JSON 格式。

目前，OAuth 服务提供了以下接口:

---

- `/services/api/get_events`: 获取用户事件流上的指定数量的事件。

需要特殊授权: **否**

参数:

1. `serviceId`: 服务 ID （必需）
2. `secretKey`: 服务 secretKey（必需）
3. `userId`: 用户 ID （必需）
4. `eventCount`: 事件数量，介于 0 至 1000 之间的整数 （必需）

---

- `/services/api/add_event`: 向用户事件流上添加事件。

需要特殊授权: **是**

参数:

1. `serviceId`: 服务 ID （必需）
2. `secretKey`: 服务 secretKey（必需）
3. `userId`: 用户 ID （必需）
4. `eventTitle`: 事件标题 （必需）
5. `eventDescription`: 事件描述 （必需）
6. `eventTime`: 事件时间 （可选）

---

以上接口中，需要特殊授权的接口请联系管理员说明使用场景，获得授权。
