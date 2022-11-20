### interface for prospect
|api|method|frontend|backend|
|:---:|:---:|:---:|:---:|
|/send_code|post|[CodeInfo](#CodeInfo)|[CodeResult](#CodeResult)|
|/waterfall|get|/|[WaterFall](#WaterFall)|
|/subscribe|post|[SubscribeInfo](#SubscribeInfo)|[SubscribeResult](#SubscribeResult)|
|/get_user_subscribe|post|[GetSubscribeInfo](#GetSubscribeInfo)|[GetSubscribeResult](#GetSubscribeResult)|
|/get_university|get|/|[UniversityResult](#UniversityResult)|
|/get_department|post|[GetDepartmentInfo](#GetDepartmentInfo)|[DepartmentResult](#DepartmentResult)|
|/post/:post_code|get|/|[PostContent](#PostContent)|
|/assets/:source_link|get|/|[SourceContent](#SourceContent)|

#### CodeInfo
``` rust
struct CodeInfo {
  // code from wx.login()
  // if access_token cached, send null String.
  code: String,
    
  // open_id received from third party server,
  // fill by null String if is first request.
  open_id: String,

  // access_token cached from third party server
  // send null String if no access_token cached.
  access_token: String,
}
```

##### 细节：

- 检查本地是否存储 `access_token`，若是，将与 `access_token` 一同存储的 `open_id` 发送至服务器，其中 `code` 值推荐为空串。
- 若 `access_token` 未存储，判定为第一次登录，`code` 填写 `wx.login()` 返回的 code 值，其余字段留空。

#### CodeResult

``` rust
struct CodeResult {
  err_code: i32,
  message: String,
  open_id: String,
  access_token: String,
}
```

##### 细节

- 若 `err_code` 为 `0` 代表成功，`open_id` 和 `access_token` 均为有效值，小程序端需要更新并将这两个字段存储在本地。**注意**：对于使用 `access_token` 进行 token 验证的登录请求同样需要更新本地 `access_token` 字段。
- 若 `err_code` 非 `0`，`open_id` 与 `access_token` 两字段无效。

#### WaterFall

``` rust
struct WaterFall {
  err_code: i32,
  message: String,
  items: Vec<WaterFallItem>,
}

struct WaterFallItem {
  // all format of link is URL.
  img_source_link: String,
  title: String,
  post_id: u64,
}
```

#### SubscribeInfo
``` rust
struct SubscribeInfo {
  info: Vec<SubscribeDetail>,
  open_id: String,
  access_token: String,
}
```

##### SubscribeDetail

``` rust
struct SubscribeDetail {
  school_code: u32,
  department_code: u32,
  oper: u16,
}
```

oper:

- 0: 订阅
- 1: 取消订阅

#### SubscribeResult

``` rust
struct SubscribeResult {
  err_code: i32,
  message: String,
}
```
#### GetSubscribeInfo

``` rust
struct GetSubscribeInfo {
  access_token: String,
  open_id: String,
}
```

#### GetSubscribeResult

``` rust
struct GetSubscribeResult {
  err_code: i32,
  info: std::collections::HashMap<String, Vec<String>>,
}
```

#### UniversityResult

``` rust
struct UniversityResult {
  err_code: i32,
  message: String,
  universities: std::collections::HashMap<String, u32>,
}
```

#### GetDepartmentInfo
``` rust
struct GetDepartmentInfo {
  university_code: u32,
}
```

#### DepartmentResult
``` rust
struct DepartmentResult {
  err_code: i32,
  message: String,
  departments: std::collections::HashMap<String, u32>,
}
```

#### PostContent
``` rust
struct PostContent {
  // TODO: more detail
  title: String
  date: TimeFormat,
  author: String,
  content: String,
}
```

#### SourceContent
``` rust
struct SourceContent {
  err_code: i32,
  message: String,
  content: Vec<u8>,
}
```



#### Error

``` rust
pub enum Error {
  // wechat defined error
  Success,

  WechatServerBusy,
  InvalidCode,
  RequestTooFast,
  HighRiskUser,

  // self defined error

  /// Invalid Json from wechat server
  InvalidJson,
  /// Cannot connect to wechat server
  NetworkToWechatErr,
  /// Reply from wechat server not contains union_id
  UnionIdNotFound,
  /// Access token expired
  TokenExpired,
  /// Reply from wechat server not contains openid
  OpenIdNotFound,
  /// Reply from internal database error
  DatabaseErr,
  /// Unknown error
  UnknownErr,
}

impl Into<i32> for Error {
  fn into(self) -> i32 {
    match self {
      // wechat defined error
      Error::Success => 0,

      Error::WechatServerBusy => -1,
      Error::InvalidCode => 40029,
      Error::RequestTooFast => 45011,
      Error::HighRiskUser => 40226,

      // self defined error
      Error::InvalidJson => 101,
      Error::NetworkToWechatErr => 102,
      Error::UnionIdNotFound => 103,
      Error::TokenExpired => 104,
      Error::OpenIdNotFound => 105,
      Error::DatabaseErr => 106,
      Error::UnknownErr => 999,
    }
  }
}
```

