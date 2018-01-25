# Platform OpenAPI — 平台开放接口

## 场景及用途

## 接口鉴权

参阅[《消息接口文档》](https://qiyukf.com/newdoc/html/message_interface.html#数据校验)

## 接口协议

接口属性描述了接口的方法、地址和`Content-Type`，Payload部分描述了接口的协议内容。
### 第三方登录
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/account/login?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{
	"userName":"qiyu_merchant@163.com",
	"code":"qiyu_merchant",
	"shopCode":"merchant"}
```

接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| userName | 是 |登录的平台所属企业中的账号 |
| code | 是 | 登录平台所属企业code，商户二级域名前缀，商户登录域名为qiyu_merchant.qiyukf.com，code即为qiyu_merchant|
| shopCode | 是 | 登录平台所属企业shopCode，商户二级域名前缀‘_’后的内容，商户登录域名为qiyu_merchant.qiyukf.com，shopCode即为merchant|

>
* code和shopCode必填其一，都存在的情况下，优先以code为准;

响应：

```json
{
	"code":200,
	"result":{redirectUrl:”http://qiyu_merchant.qiyukf.com?thirdToken=ahudgsabdasbdahsbdasbdasbdu”},
	"message":"success",
	"total":0
}
```

响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果，一个如下对象{redirectUrl:”http://qiyu_merchant.qiyukf.com?thirdToken=ahudgsabdasbdahsbdasbdasbdu”}。直接打开对象中的url可完成一次直接登录|
| message | 状态信息描述 |
| total | 此处无意义 |

>
* 的redirectUrl可以使用一次登录，直接跳转至这个链接即可，使用一次后失效


### 获取平台下商户列表
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/list?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{    "offset":5,    "limit":4}```
接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| offset | 是 | 获取列表分页偏移量(偏移量，并不是页数) |
| limit | 是 | 获取列表分页大小 |

响应：

```json
{    "code":200,    "result":[        {            "id":102146,            "domain":"qiyu-merchant2",            "name":"merchant2",            "seatNumber":33,            "account":"qiyu-merchant2@163.com",            "contact":"13800000000",            "status":1        },        {            "id":102145,            "domain":"qiyu-merchant3",            "name":" merchant3",            "seatNumber":1,            "account":"qiyu-merchant3@163.com",            "contact":"13588895902",            "status":1        },        {            "id":102144,            "domain":"qiyu-merchant4 ",            "name":"merchant4",            "seatNumber":34,            "account":"qiyu-merchant4@163.com ",            "contact":"13800000000",            "status":1        },        {            "id":102143,            "domain":"qiyu-merchant5",            "name":"merchant5",            "seatNumber":1,            "account":"qiyu-merchant5@163.com ",            "contact":"13588895902",            "status":1        }    ],    "message":"success",    "total":27}
```响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果，一个jsonArray数组，内部是获取的商户列表 |
| message | 状态信息描述 |
| total | 表示平台下总共有多少商户 |

### 获取商户信息
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/query?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{    "id":102146,
    "shopCode":"merchant2"}
```

接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| id | 是 | 启用商户id，与商户列表接口拉取到的数据中id对应 |
| shopCode | 否 | 商户shopCode，二级域名前缀'_'后的字串 |
>
* id与shopCode两个选一个上传即可，但必须上传一个，当id与shopCode同时上传时，以id为有限参考

响应：

```json
{    "id":102146,    "domain":"qiyu-merchant2",    "name":"merchant2",    "seatNumber":33,    "account":"qiyu-merchant2@163.com",    "contact":"13800000000",    "status":1}```

响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果，一个商户对象 |
| message | 状态信息描述 |
| total | 此处无意义 |

### 创建商户
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/create?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{    "password":"e10adc3949ba59abbe56e057f20f883e",    "domain":"qiyu-merchant",    "contact":"13588895902",    "name":"七鱼商户1号",    "seatNumber":1,    "account":"qiyu-merchant@163.com",    "portrait":"/9j/4AAQSkZJRg...省略若干字符...LE9q0jeKsTQ1kf/2Q=="}
```

接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| domain | 是 | 商户二级域名前缀，不需要带平台前缀，即是前文提到的shopCode.如平台code为qiyu，商户code为qiyu-merchant,那商户的domain(shopCode)即为merchant |
| name | 是 | 商户名，50个字符以内 |
| seatNumber | 是 | 坐席数，不能超过剩余平台剩余总坐席数，可以为0 |
| account | 是 | 商户超管超管账号，格式为邮箱，最好是可用邮箱 |
| contact | 否 | 联系方式，50个字符以内 |
| password | 是 | 商户超管登录密码，MD5(32位小写)加密后发送 |
| portrait | 否 | 头像图片文件内容base64运算后的字符串 |
>
* 由于图像图片上传速度较慢，所以这个接口需要上传头像时的超时限制请放宽一些

响应：

```json
{    "code":200,    "result":102143,    "message":"success",    "total":0}```

响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果,此处成功返回新增商户id |
| message | 状态信息描述 |
| total | 此处无意义 |

### 修改商户
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/update?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{    "contact":"18668021155",    "name":"七鱼商户2号",    "id":"102106",    "seatNumber":2,    "account":"qiyu-merchant2@163.com",    "portrait":"/9j/4AAQSkZJRg...省略若干字符...LE9q0jeKsTQ1kf/2Q=="}```

接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| id | 是 | 商户id,与商户列表接口拉取到的数据中id对应 |
| shopCode | 否 | 标识商户，商户二级域名前缀'_'后的字串，与id只需传一个 |
| name | 否 | 商户名，50个字符以内 |
| seatNumber | 否 | 坐席数，增加的数量不能超过剩余平台剩余总坐席数，可以为0 |
| account | 否 | 商户超管超管账号，格式为邮箱，最好是可用邮箱 |
| contact | 否 | 联系方式，50个字符以内 |
| portrait | 否 | 头像图片文件内容base64运算后的字符串 |
>
* 由于图像图片上传速度较慢，所以这个接口需要上传头像时的超时限制请放宽一些
* 当id与shopCode同时存在时，优先用id标识商户

响应：

```json
{    "code":200,    "result":"success",    "message":"success",    "total":0}```

响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果,此处成功为success字符串 |
| message | 状态信息描述 |
| total | 此处无意义 |

### 商户启用
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/enable?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{    "id": 102106}```

接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| id | 是 | 商户id,与商户列表接口拉取到的数据中id对应 |
| shopCode | 否 | 标识商户，商户二级域名前缀'_'后的字串，与id只需传一个 |
>
* 当id与shopCode同时存在时，优先用id标识商户

响应：

```json
{    "code":200,    "result":"success",    "message":"success",    "total":0}```

响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果,此处成功为success字符串 |
| message | 状态信息描述 |
| total | 此处无意义 |

### 商户停用
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/disable?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{    "id": 102106}```

接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| id | 是 | 商户id,与商户列表接口拉取到的数据中id对应 |
| shopCode | 否 | 标识商户，商户二级域名前缀'_'后的字串，与id只需传一个 |
>
* 当id与shopCode同时存在时，优先用id标识商户

响应：

```json
{    "code":200,    "result":"success",    "message":"success",    "total":0}```

响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果,此处成功为success字符串 |
| message | 状态信息描述 |
| total | 此处无意义 |

### 修改商户密码
POST 请求为：

```
POST https://qiyukf.com/platform/api/openapi/merchant/changePassword?appKey={appKey}&time={time}&checksum={checksum}
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{    "id": 102106,	"password":"e10adc3949ba59abbe56e057f20f883e"}```

接口参数说明如下:

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| id | 是 | 商户id,与商户列表接口拉取到的数据中id对应 |
| shopCode | 否 | 标识商户，商户二级域名前缀'_'后的字串，与id只需传一个 |
| password | 是 | 商户超管登录密码，MD5(32位小写)加密后发送 |
>
* 当id与shopCode同时存在时，优先用id标识商户

响应：

```json
{    "code":200,    "result":"success",    "message":"success",    "total":0}```

响应参数说明如下:

| 参数 | 参数说明 |
| :-----:  | :----- |
| code |返回状态码，见状态码表|
| result | 返回结果,此处成功为success字符串 |
| message | 状态信息描述 |
| total | 此处无意义 |
