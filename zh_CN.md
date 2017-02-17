# HyperIdentity SSO 开发文档

### 常量

HyperIdentity 公共服务域: `hyperidentity.ifxor.com` （仅支持 HTTPS ）。以下所有调用地址均位于此域下。

### OpenID

HyperIdentity 平台目前提供使用简单、易于集成的 OpenID 服务。

调用方法: 

首先，发起方跳转页面到 `/web/?callback=CALLBACK_URL#auth`
（其中 `CALLBACK_URL` 为认证成功后的回调地址，不可带有任何 `?` 或 `#` 参数，且其域名部分必须与来源 (Referrer) 域相同。
301 / 302 跳转不会包含 `Referer` 头，所以请使用客户端 JavaScript 或链接进行跳转。）

`CALLBACK_URL` 接收到通过 GET 方式传递的回调参数 `client_token` 后，向 `/identity/verify/verify_client_token` 发起 POST 请求
( body: `client_token=CLIENT_TOKEN`, CLIENT_TOKEN 为接收到的 client_token 回调参数 ) 并解析返回的 JSON 格式数据。

正常情况下的返回数据示例:

    {
      "err": 0,
      "msg": "OK",
      "userId": "838ba06d-d31b-4db2-8174-4ebf88388605",
      "username": "testuser"
    }

其中 `userId` 为用户唯一 ID ，`username` 为用户名。

如果 `err` 不为 0 ，说明认证失败。失败原因包含在 `msg` 中。

