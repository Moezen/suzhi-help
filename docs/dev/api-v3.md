
# 速致 API.v3 文档
------------

## API 简介
- 版本：V3.0（更新时间：2015.07)
- 简介：速致将逐渐开放一系列API提供给广大用户，以便更好地使用我们的产品。
- 使用对象：个人、企业用户。（如无特别说明，都可以使用）

## 调用规范
1. **调用地址: https://www.cdnzz.com/apiv3/json**
2. 使用 POST 方式传递参数
3. 接口返回:
    - 返回数据使用 JSON 格式
    - 数据结构如下:
```
{
  "error": 0,   # 错误码, 非0表示出错
  "msg": "",    # 简短的错误描述
  "result": {   # 请求接口返回的数据, 具体内容参考各接口的示例返回
  }
}
```


## 基础接口


-----------
#### 公共参数  

  | 参数 | 必选 | 类型 | 说明 | 
  | :----: | :----: | :---: | :-----: | 
  | user | True | string |  用户账号 email | 
  | method | True | string |  操作方法, 不指定方法或者方法名错误时返回可用方法名列表 | 
  | token | True | string |  用户的 token, 通过 FetchToken 接口获取, 其他接口均需要该参数 | 

------------
#### 获取Token
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |   True   |   string |  "FetchToken" | 
    |  secretkey |  True  |  string |  安全密钥, 可在官网 “个人信息” 页查看 | 
    |  exp   |  False |  string |  token 失效时间点, unix timestamp, 默认 10分钟后失效 | 
    |  name |  False |  string |  为 token 指定一个 name, 用于区分不同用途的 token, 默认无 | 

- **示例**:
`user=apitest@cdnzz.com&secretkey=<your secretkey>&method=FetchToken&name=test`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
    "payload": {
      "exp": 1433214164,
      "iss": "apitest@cdnzz.com",
      "jti": "bd50c2efece1ab376c30f0e26f165a9a",
      "name": "test"
    },
    "token": "eyJhbGciO..."
  }
}
```
- **说明**:
token 使用 [JWT](http://jwt.io/) 方式编码, payload 如下:
```
{
  "iss": "apitest@cdnzz.com",   # token的使用账户
  "jti": "e1197eea1ccc...",     # token id
  "name": test,         # 传入的 name
  "exp": 1432870303     # token 过期时间
}
```



------------
## 域名操作


#### 添加域名
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  |  
    | :----: | :-----: | :-----: | :------: | 
    |  method |  True    |  string  |  AddDomain | 
    |  domain |  True    |  string  |  要添加的域名 | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=AddDomain&domain=cdnzz.com`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
    "domain": "cdnzz.com",
    "icp": 0,
    "verify": 0
  }
}
```

#### 获取域名验证信息
- **说明**: 新添加的域名需要验证才可以使用,通过该接口获取验证需要用到的信息
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |   method |  True   |  string  |  "FetchVerifyInfo" | 
    |  domain  |  True   |  string  |  操作的域名   | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=FetchVerifyInfo&domain=cdnzz.com`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
    "dns_txt_record": "cdnzz-site-verification=295f4c...",  # 需要设置的 dns txt 记录值
    "domain": "cdnzz.com",
    "icp": 0,       # 域名是否已通过 ICP 备案
    "verify": 0     # 域名是否已通过验证
  }
}
```

#### 请求验证域名
- **说明**: 添加完验证信息后通过该接口发起域名验证请求，同时可根据返回信息获取域名验证状态
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |   method |  True  |   string  |   "VerifyDomain" | 
    |   domain |  True  |   string  |  操作的域名  | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=VerifyDomain&domain=cdnzz.com`
- **返回**: 对应域名的信息
```
{
  "error": 0,
  "msg": "",
  "result": {
    "domain": "cdnzz.com",
    "icp": 1,
    "verify": 1
  }
}
```

#### 列出所有域名
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |  True    |  string  |  "ListDomain" | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=ListDomain`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": [
    {
      "domain": "example1.com",
      "icp": 1,
      "verify": 1
    },
    {
      "domain": "cdnzz.com",
      "icp": 1,
      "verify": 1
    }
  ]
}
```


--------------
## 子域名操作


#### 添加子域名
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |   True   |  string  |  AddSubDomain | 
    |  domain |   True   |  string  |  操作的域名 | 
    |   host  |   True   |  string  |  要添加的子域名 | 
    |  type   |   True   |  string  |  子域名类型, A 或者 CNAME | 
    |  value  |   True   |  string  |  子域名的值, A 记录对应 IP, CNAME 记录对应域名 | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=AddSubDomain&domain=cdnzz.com&host=static&type=CNAME&value=cdn.cdnzz.com`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
    "active": 1,
    "domain": "cdnzz.com",
    "host": "static",
    "id": 4,
    "record_type": "CNAME",
    "value": "cdn.cdnzz.com"
  }
}
```

#### 删除子域名
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |   True   |  string  |  DelSubDomain | 
    |  domain |   True   |  string  |  操作的域名   | 
    |  sub_id |   True   |  string  |  删除的子域名ID(通过 ListSubDomain 获取) | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=DelSubDomain&domain=cdnzz.com&sub_id=5`
- **返回**: 被删除的子域名信息
```
{
  "error": 0,
  "msg": "",
  "result": {
    "active": 1,
    "domain": "cdnzz.com",
    "host": "img",
    "id": 5,
    "record_type": "CNAME",
    "value": "cdn.cdnzz.com"
  }
}
```

#### 列出所有子域名
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |   True   |  string  |  "ListSubDomain" | 
    |  domain |   True   |  string  |  操作的域名   | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=ListSubDomain&domain=cdnzz.com`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": [
    {
      "active": 1,
      "domain": "cdnzz.com",
      "host": "static",
      "id": 4,
      "record_type": "CNAME",
      "value": "cdn.cdnzz.com"
    },
    {
      "active": 1,
      "domain": "cdnzz.com",
      "host": "img",
      "id": 5,
      "record_type": "CNAME",
      "value": "cdn.cdnzz.com"
    }
  ]
}
```

#### 更改子域名
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |  True    |   string |  "ModifySubDomain" | 
    |  domain |  True    |   string |  操作的域名  | 
    |  host   |  True    |   string |  更改的子域名 | 
    |  sub_id |  True    |   int    |  操作的子域名ID(通过 ListSubDomain 获取) | 
    |  type  |  True    |   string |  新的子域名类型, A 或者 CNAME |
    |  value  | True | 新的子域名值, A 记录对应 IP, CNAME 记录对应域名 |

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=ModifySubDomain&domain=cdnzz.com&host=static&sub_id=4&type=A&value=8.8.8.8`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
    "active": 1,
    "domain": "cdnzz.com",
    "host": "static",
    "id": 4,
    "record_type": "A",
    "value": "8.8.8.8"
  }
}
```

#### 激活子域名
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |   True   |  string   |  "ActiveSubDomain" | 
    |  domain |   True   |  string   |  操作的域名  | 
    |  sub_id |   True   |  string   |  操作的子域名ID(通过 ListSubDomain 获取) | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=ActiveSubDomain&domain=cdnzz.com&sub_id=4`
- **返回**: 操作后的子域名信息
```
{
  "error": 0,
  "msg": "",
  "result": {
    "active": 1,
    "domain": "cdnzz.com",
    "host": "static",
    "id": 4,
    "record_type": "A",
    "value": "8.8.8.8"
  }
}
```

#### 使子域名回源
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |  True    |  string  |  "InactiveSubDomain" | 
    |  domain |  True    |  string  |  操作的域名 | 
    |  sub_id |  True    |  string  |  操作的子域名ID(通过 ListSubDomain 获取) | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=InactiveSubDomain&domain=cdnzz.com&sub_id=4`
- **返回**: 操作后的子域名信息
```
{
  "error": 0,
  "msg": "",
  "result": {
    "active": 0,
    "domain": "cdnzz.com",
    "host": "static",
    "id": 4,
    "record_type": "A",
    "value": "8.8.8.8"
  }
}
```


--------------
## 内容管理


#### 预加载
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |  True    |  string  |  " AddPreload" | 
    |  url    |  True    |  string  |  需要预加载的 url | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=AddPreload&url=http://static.cdnzz.com/logo.png`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
    "status": "OK",
    "url": "http://static.cdnzz.com/logo.png"
  }
}
```

#### 清除缓存
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  method |   True   |  string  |  "PurgeCache" | 
    |   url   |   True   |  string  |  需要清除缓存的 url | 

- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=PurgeCache&url=http://static.cdnzz.com/logo.png*
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
    "status": "OK",
    "url": "http://static.cdnzz.com/logo.png"
  }
}
```


------------
## 数据查询


#### 获取流量数据
- **说明**: 查询指定时间区间内的流量数据
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  domain |  True  |  string  |  指定查询的域名, 多个域名使用 ',' 分隔, 默认查询账户下所有域名的总流量 | 
    |  sub_name |  True |  string  | 指定查询的子域名, 默认查询 domain 下都所有子域名, 多个子域名使用 ',' 分隔,　如果指定了多个 domain 则不可指定 sub_name | 
    |  start_day |  True |  string  |  开始日期, %Y%m%d 格式, 默认查询当天, 示例: 20150101 | 
    |  end_day   |  True |  string  |  结束日期, 格式同 start_day, 默认查询１天的数据 | 

- **注意**:
    - 返回数据的时间粒度会自动根据查询时间范围进行选择, 有 分钟/小时/天/月 几种粒度
- **示例**:
`user=apitest@cdnzz.com&token=<token>&method=FetchTraffic&domain=cdnzz.com&sub_name=img&start_day=20150101&end_day=20150110`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": [
    {
      "compress": 0,    # 压缩节省的流量, 单位: bit
      "datetime": "20150101000000",     # 时间格式 yyyymmddHHMMSS
      "hit": 190454,    # CDN 命中次数
      "miss": 9398,     # CDN 未命中次数
      "traffic_hit": 20873426879,   # 命中产生的流量, 单位: bit
      "traffic_miss": 1591256206    # 未命中产生的流量, 单位: bit
    },
    {
      "compress": 0,
      "datetime": "20150102000000",
      "hit": 188098,
      "miss": 9789,
      "traffic_hit": 20686018210,
      "traffic_miss": 1523263729
    },
    ...
}
```


#### 获取带宽数据
- **说明**: 查询指定时间区间内的带宽数据
- **参数**:  

    |  参数   |   必选   |  类型    |  说明  | 
    | :----: | :-----: | :-----: | :------: | 
    |  domain |   True   |   string |  指定查询的域名, 多个域名使用 ',' 分隔, 默认查询账户下所有域名的总带宽 | 
    |  sub_name |  True  |   string |  指定查询的子域名, 默认查询 domain 下都所有子域名, 多个子域名使用 ',' 分隔,　如果指定了多个 domain 则不可指定 sub_name | 
    |  start_day |  True |  string |  开始日期, %Y%m%d 格式, 默认查询当天, 示例: 20150101 | 
    |  end_day   |  True |  string |  结束日期, 格式同 start_day, 默认查询１天的数据 | 

- **注意**:
    - 返回数据的时间粒度会自动根据查询时间范围进行选择, 有 5m/30m/2h/1d 几种粒度
- **示例**:
`user=apitest@cdnzz.com&token=<token>&method=FetchBandwidth&domain=cdnzz.com&sub_name=img&start_day=20150101&end_day=20150110`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": [
    {
      "bandwidth": 151087641,   # 使用的带宽, 单位: bit
      "datetime": "20150101000000"  # 时间格式 yyyymmddHHMMSS
    },
    {
      "bandwidth": 78896124,
      "datetime": "20150101003000"
    },
    {
      "bandwidth": 91646283,
      "datetime": "20150101010000"
    },
    {
      "bandwidth": 49301379,
      "datetime": "20150101013000"
    },
    ...
}
```

####  获取线路的 IP
- **说明**: 查询当前 CDN 线路所覆盖的 IP
- **参数**:

    |  参数名    |  必选    |  类型    |  备注    |
    | :----: | :-----: | :-----: | :------: | 
    |  method |  True    |  string  |  "Fetchcdnips" |

- **示例**:
`user=apitest@cdnzz.com&token=<token>&method=Fetchcdnips`
- **返回**:
```
{
    "msg": "",
    "response": ["8.8.8.8", "9.9.9.9"],
    "result": "Success",
}
```


## 错误码说明
- 错误码分类如下:
```
10000: "Internal Server Error",     # 未知错误
10200: "Authentication Failed",     # 认证出错
10260: "Invalid Token",             # Token 错误, 当返回此错误号时应该尝试重新生成 token
10300: "Param Error",               # 请求参数出错
10400: "Authorization Failed",      # 权限出错
10500: "Operation Failed"           # 执行操作失败
```
- 10X\*\* 表示同一类错误，如 102\*\* 均表示认证出错，msg 字段会有具体的错误说明


## SDK
- Python SDK <https://github.com/GridSafe/grid-sdk-python2-v3>
- PHP SDK <https://github.com/GridSafe/grid-sdk-php-v3>

