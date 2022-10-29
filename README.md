### interface for prospect
|api|method|frontend|backend|
|:---:|:---:|:---:|:---:|
|/send_code|post|[CodeInfo](#CodeInfo)|[CodeResult](#CodeResult)|
|/waterfall|get|/|[WaterFall](#WaterFall)|
|/subscribe|post|[SubscribeInfo](#SubscribeInfo)|[SubscribeResult](#SubscribeResult)|
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

#### CodeResult
``` rust
struct CodeResult {
  err_code: i32,
  message: String,
  open_id: String,
  access_token: String,
}
```

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
  school_code: Vec<u32>,
  open_id: String,
  access_token: String,
}
```

#### SubscribeResult
``` rust
struct SubscribeResult {
  err_code: i32,
  message: String,
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
