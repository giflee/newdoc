# 网易七鱼企业信息对接开发指南

## 简介

### 概述

网易七鱼系统可以与企业 CRM 系统进行对接。

对接分**轻量对接**与**接口对接**两种方式。

* **轻量对接**是指通过 Web / iOS / Android SDK 发起会话时，将访客的信息作为参数传递给网易七鱼系统。
* **接口对接**是指企业提供一系列 HTTP/HTTPS 接口，由网易七鱼系统调用，获取访客的相关信息，并可以实现数据同步、访客身份验证等高级功能。

轻量对接的数据由企业产品客户端（或网站）将数据提交给网易七鱼系统，数据将展现在客服工作界面的当前会话和历史会话的“用户资料”标签下。接口对接的数据由企业接口提供，由网易七鱼系统客服端调用，数据将展现在客服工作界面的当前会话和历史会话的“更多信息”标签下。

轻量对接和接口对接的具体说明，请分别阅读**轻量 CRM 对接**和** CRM 接口对接**章节。两种对接方式的对比，请阅读**对接方式对比与建议**章节。
    
### 文档约定

**访客**：指使用企业产品的用户，例如使用企业 App 产品的用户，或访问企业网站或 Web 产品的用户。

**企业接口**：指接口对接方式下，由企业开发并提供给网易七鱼系统的 HTTP/HTTPS 接口。

**客服工作台**、**客服工作界面**：都是指网易七鱼系统提供给客服使用的工作环境，即客服登录网易七鱼系统后使用的 Web 界面。

**Web SDK**：指网易七鱼系统提供的 Web 端开发工具包，供企业将网易七鱼功能嵌入到官网或 Web 产品中。主要包含一个在线获取的 JS 文件。

**iOS SDK**：指网易七鱼系统提供的 iOS 平台开发工具包，供企业将网易七鱼功能嵌入到 iOS 产品中。主要包含一个 .a 静态库，和一系列依赖的头文件、资源文件。

**Android SDK**：指网易七鱼系统提供的 Android 平台开发工具包，供企业将网易七鱼功能嵌入到 Android 产品中。主要包含多个 .jar 包，和一系列依赖的资源文件。

**JSON**：企业 CRM 系统与网易七鱼系统对接，数据传输时使用 JSON 格式。多数情况下数据都保存在一个数组中，数组中一个元素表示一个数据项。数据项包含的字段，有些是必选的，有些是可选的，请仔细阅读每个接口的数据定义。参考：[JSON介绍](http://www.json.org/json-zh.html)、[JSON在线校验工具](http://json.cn/)。

## 轻量&nbsp;CRM&nbsp;&nbsp;对接

轻量 CRM 对接是指通过 Web / iOS / Android SDK 发起会话时，可以将访客的信息作为参数传递给网易七鱼系统。

### Web&nbsp;SDK&nbsp;调用

调用`ysf.config()`时，将包含用户信息的 JSON 传递给网易七鱼系统。

```javascript
ysf.config({
    "uid":"zhangsan",
    "data":JSON.stringify([
        {"key":"real_name", "value":"土豪"},
        {"key":"mobile_phone", "hidden":true},
        {"key":"email", "value":"13800000000@163.com"},
        {"index":0, "key":"account", "label":"账号", "value":"zhangsan" , "href":"http://example.domain/user/zhangsan"},
        {"index":1, "key":"sex", "label":"性别", "value":"先生"},
        {"index":5, "key":"reg_date", "label":"注册日期", "value":"2015-11-16"},
        {"index":6, "key":"last_login", "label":"上次登录时间", "value":"2015-12-22 15:38:54"}
    ])
});
```

**注意：**```data```字段的内容需使用```JSON.stringify()```进行序列化。为保持良好的兼容性，若要支持较低版本的浏览器（如IE8及更低版本）则需引入第三方JSON库，如 [JSON3](http://bestiejs.github.io/json3/)。

### iOS&nbsp;SDK&nbsp;调用

创建一个`QYUserInfo`实例，将包含用户信息的 JSON 字符串填充到`QYUserInfo.data`。调用`setUserInfo`时将此`QYUserInfo`实例作为参数传递给网易七鱼 SDK。

```c
QYUserInfo *userInfo = [[QYUserInfo alloc] init];
userInfo.userId = @"uid";
userInfo.data = @"[{\"key\":\"real_name\", \"value\":\"土豪\"},"
                  "{\"key\":\"mobile_phone\", \"hidden\":true},"
                  "{\"key\":\"email\", \"value\":\"13800000000@163.com\"},"
                  "{\"index\":0, \"key\":\"account\", \"label\":\"账号\", \"value\":\"zhangsan\", \"href\":\"http://example.domain/user/zhangsan\"},"
                  "{\"index\":1, \"key\":\"sex\", \"label\":\"性别\", \"value\":\"先生\"},"
                  "{\"index\":5, \"key\":\"reg_date\", \"label\":\"注册日期\", \"value\":\"2015-11-16\"},"
                  "{\"index\":6, \"key\":\"last_login\", \"label\":\"上次登录时间\", \"value\":\"2015-12-22 15:38:54\"}]";

[[QYSDK sharedSDK] setUserInfo:userInfo];
```

### Android&nbsp;SDK&nbsp;调用

创建一个`YSFUserInfo`实例，将包含用户信息的 JSON 字符串填充到`YSFUserInfo.data`。调用`Unicorn.setUserInfo()`时将此`YSFUserInfo`实例作为参数传递给网易七鱼 SDK。

```java
YSFUserInfo userInfo = new YSFUserInfo();
userInfo.userId = "uid";
userInfo.data = "[{\"key\":\"real_name\", \"value\":\"土豪\"},
                  {\"key\":\"mobile_phone\", \"hidden\":true},
                  {\"key\":\"email\", \"value\":\"13800000000@163.com\"},
                  {\"index\":0, \"key\":\"account\", \"label\":\"账号\", \"value\":\"zhangsan\" , \"href\":\"http://example.domain/user/zhangsan\"},
                  {\"index\":1, \"key\":\"sex\", \"label\":\"性别\", \"value\":\"先生\"},
                  {\"index\":5, \"key\":\"reg_date\", \"label\":\"注册日期\", \"value\":\"2015-11-16\"},
                  {\"index\":6, \"key\":\"last_login\", \"label\":\"上次登录时间\", \"value\":\"2015-12-22 15:38:54\"}]";

Unicorn.setUserInfo(userInfo);
```

### 参数说明

网易七鱼系统轻量对接中的参数，不论是 Web SDK、iOS SDK、还是 Android SDK，都由一个用户唯一性标识`uid`和一个表示用户信息的 JSON 数组`data`组成。

在 Web SDK 中`uid`和`data`都直接出现在 JSON 中。iOS SDK 和 Android SDK 中，各定义了一个保存用户信息的结构体：iOS 中为`QYUserInfo`，Android 中为`YSFUserInfo`。iOS SDK 中，`QYUserInfo.userId`成员为用户唯一性标识字符串，`QYUserInfo.data`成员为表示用户信息的 JSON 字符串。Android SDK 中，`YSFUserInfo.userId`成员为用户唯一性标识字符串，`YSFUserInfo.data`成员为表示用户信息的 JSON 字符串。

下文参数说明中的`uid`分别对应 Web SDK 中的 JSON 字段 `uid`、iOS SDK 中的`QYUserInfo.userId`、Android SDK 中的`YSFUserInfo.userId`；`data`分别对应 Web SDK 中的 JSON 字段 `data`、iOS SDK 中的`QYUserInfo.data`、Android SDK 中的`YSFUserInfo.data`。

| **<div style="width:60px">参数</div>**  | **类型** | **必须** | **说明** |
| :-- | :-- | :--: | -- |
| **`uid`** | String | 是 | 用户唯一性标识。 |
| **`data`** | Array / String | 是 | 用一个数组（或表示 JSON 数组的字符串），表示要显示在客服端的扩展信息。 |

其中`data`字段用一个数组（iOS / Android SDK 中是一个表示 JSON 数组的字符串）描述用户的详细信息，数组中每个元素代表一个数据项。数据项以`<key, value>`对的形式为基础，增加了额外的字段以控制显示样式。数据项定义如下：

| **<div style="width:60px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。其中`real_name`、`mobile_phone`、`email`为保留字段，分别对应客服工作台用户信息中的“姓名”、“手机”、“邮箱”这三项数据。保留关键字对应的数据项中，`index`、`label`属性将无效，其显示顺序及名称由网易七鱼系统指定。 |
| **`value`** | Mixed | 是 | 该数据显示的值，类型不做限定，根据实际需要进行设定。 |
| **`label`** | String | 是 | 该项数据显示的名称。 |
| **`index`** | Int | 否 | 用于排序，显示数据时数据项按`index`值升序排列；不设定`index`的数据项将排在后面；`index`相同或未设定的数据项将按照其在 JSON 中出现的顺序排列。 |
| **`href`** | String | 否 | 超链接地址。若指定该值，则该项数据将显示为超链接样式，点击后跳转到其值所指定的 URL 地址。 |
| **`hidden`**| Boolean | 否 | 仅对`mobile_phone`、`email`两个保留字段有效，表示是否隐藏对应的数据项，`true`为隐藏，`false`为不隐藏。若不指定，默认为`false`不隐藏。 |

**注意：** `real_name`、`mobile_phone`、`email`三个保留字段的特别说明：

* 若使用保留字段，数据将对应显示在“姓名”、“手机”、“邮箱”的位置上，顺序、名称不能指定；
* 若使用保留字段，“姓名”、“手机”、“邮箱”对应的数据将不可编辑，以传递的数据为准；在不使用保留字段的情况下，这三项内容以客服填写的为准，并且可以修改；
* 其中`mobile_phone`、`email`两项可以通过`hidden`字段隐藏，`real_name`不可隐藏；
* 如果既想通过轻量对接提供“姓名”、“手机”、“邮箱”这三项数据，又希望客服可以修改、维护这些信息，可以避开保留关键字，用不同的`key`来提供数据；这在访客改变了联系方式，但没有及时更新用户信息的情况下非常有用。

**注意：** 提交的数据将根据预设的样式直接显示在网易七鱼页面相应的位置上。如果数据敏感、保密性要求高，应当在提交时就做**脱敏**处理。网易七鱼系统不会对数据做额外的处理。


## 企业&nbsp;CRM&nbsp;接口对接

### 概要

企业CRM接口由企业开发者开发实现，并填写在七鱼后台的“设置--信息对接-CRM信息对接”中；当用户接入时，七鱼会调用相应接口，使用轻量对接中传递的uid；开发者再七鱼后台设置的appid以及获取token接口中拿到的token，去请求获取用户信息等接口，并将开发者的正确返回展示在当前客服聊天页面右侧的“更多信息”标签下（在“历史咨询”中也适用）。

企业实现这些接口，可以为网易七鱼系统提供用户信息、订单信息等 CRM 数据，以便客服在工作时方便地获得丰富的信息，更好的提供服务。

企业接口为一系列 HTTP （或 HTTPS）接口，由网易七鱼系统发起调用。网易七鱼系统与企业 CRM 系统通过这些接口进行数据交换，数据通常以 JSON 格式展现。即：

* 网易七鱼系统调用企业接口时，通常是向企业接口 POST 一段描述请求内容的 JSON，企业发送该请求会自动发送两遍，第一个请求是OPTION类型，第二个请求是POST类型；（两个请求都需要支持跨域访问）
* 企业接口返回数据给七鱼系统时，也返回一段包含所请求的数据的 JSON。

数据格式请仔细阅读每个接口的说明。

为保证数据的安全、并考虑一定的扩展性，企业需提前给网易七鱼系统分配`appid`、`appsecret`用于接口调用时的认证。认证方式见下。

本文档中的接口地址都为相对路径。接口完整的 URL 需要在网易七鱼管理端进行设置，管理员后台登录——设置——信息对接。

### 跨域访问

除`获取 token`、`验证用户身份`接口外，其他接口都由网易七鱼系统客服端前端发起调用，所以这些接口需要支持**跨域访问**。接口的响应头域(Header)中需要添加以下参数：

```
Access-Control-Allow-Headers: origin, x-csrftoken, content-type, accept, x-auth-code, X-App-Id, X-Token
Access-Control-Allow-Method: POST, GET, OPTIONS
Access-Control-Allow-Origin: https://xxx.qiyukf.com,http://xxx.qiyukf.com
```

其中`Access-Control-Allow-Origin`中的`xxx`应该替换为企业在网易七鱼系统上申请的二级域名（或者可以直接设置允许全部 Access-Control-Allow-Origin: *）。

更多信息请参考 [W3C Recommendation: Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/)。

### 接口认证方式

接口认证方式有两种可选：

* 若企业**提供**获取token并且通过该接口可以获取到token时，则后续所有请求通过`appid`、`token`来确认请求的合法性（如本文档所示）；
* 若企业**不提供**获取 token 并且通过该接口获取到的响应为空时，则所有请求都直接用`appsecret`当做`token`来使用。


认证参数传递方式也有两种可选：

* 放在request body的JSON中：参数的键名为`"appid"`、`"token"`（如本文档所示）；
* 放在request的头域(Header)中：参数的域名为`"X-App-Id"`、`"X-Token"`。
 
### 访客身份识别
七鱼通过用户唯一标识来识别访客身份，不同的接入渠道中所使用的用户唯一标识有所区别：

#### Web接入
Web将用户发起会话时上报的uid作为用户唯一标识，在接入七鱼的网页中，调用ysf.config()函数，将uid传给七鱼，示例如下：
```
ysf.config({
		uid:”123456”
	});
```
#### 移动端SDK接入
Android SDK需在发起会话时上报uid作为用户唯一标识，上报方法:创建一个YSFUserInfo实例，将包含用户信息的 JSON 字符串填充到YSFUserInfo.data。调用Unicorn.setUserInfo()时将此YSFUserInfo实例作为参数传递给网易七鱼 SDK。示例如下：
```
YSFUserInfo userInfo = new YSFUserInfo();
userInfo.userId="uid";
Unicorn.setUserInfo(userInfo);
```

iOS SDK需在发起会话时上报uid作为用户唯一标识，上报方法:创建一个QYUserInfo实例，将包含用户信息的 JSON 字符串填充到QYUserInfo.data。调用setUserInfo时将此QYUserInfo实例作为参数传递给网易七鱼 SDK。示例如下：
```
QYUserInfo *userInfo = [[QYUserInfo alloc] init];
userInfo.userId = @"uid";
[[QYSDK sharedSDK] setUserInfo:userInfo];
```

#### 微信接入
使用授权方式完成的微信接入，七鱼会将访客微信OpenID作为用户唯一标示。此种方式接入，需要对接企业维护OpenID与企业原有用户体系的对应关系。
使用H5页面嵌入方式完成的微信接入，由于七鱼无法获取到OpenID，与Web接入类似的，需要通过ysf.config()函数，将访客uid传给七鱼。

#### 电话接入
呼叫中心将以访客呼入电话号码作为用户唯一标识。

### 获取&nbsp;token

GET `/get_token`

与其他接口不同，获取 token 接口为 GET 请求。该接口由网易七鱼系统后端服务器调用，并根据返回的有效期决定调用时机。

#### 请求

| **参数** | **类型** | **必须** | **说明** |
| :-- | :-- | :--: | -- |
| **`appid`** | String | 是 | 企业分配给网易七鱼系统的 appid |
| **`appsecret`** | String | 是 | 企业分配给网易七鱼系统的 appsecret |

#### 响应

接口应该返回一段包含 token 和有效期的 JSON。
```javascript
{
    "rlt": 0,
    "token": "qiyukf_security_token",
    "expires": 7200000
}
```

| **<div style="width:80px">字段</div>**  | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`rlt`** | Int / String | 是 | 表明接口调用是否成功。值为`0`时表示接口调用成功。关于错误码的详细描述，见Appendix I节。 |
| **`token`** | String | 是 | 后续接口调用时使用的有效 token。 |
| **`expires`** | Int | 否 | 表示此token离过期剩余的 **毫秒数**（非过期时间戳）。若不指定、或小于等于零，则默认为 2 小时。token失效后，其他接口应返回错误码`2`。 |

### 获取用户信息

POST `/get_user_info` 在线会话获取用户信息

POST `/get_call_user_info` 呼叫中心获取电话用户信息

网易七鱼系统在需要获取访客详细信息时，会调用该接口。该接口请求类型不同于token，前端会自动发送两次请求，第一次的请求类型是OPTION，第二次的请求类型是POST，且POST提交数据的方式为`application/json`。

返回的数据将显示在“当前会话”中右侧用户资料区的“更多信息”标签页下，以及“历史会话”中右侧会话详情区的“更多信息”标签页下。

用户的唯一性标识会被作为请求内容 POST 到该接口。

#### 请求

```javascript
{
    "appid": "qiyukf",
    "token": "qiyukf_security_token",
    "userid": "zhangsan"
}
```

| **参数** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`appid`** | String | 是 | 企业分配给网易七鱼系统的 appid。 |
| **`token`** | String | 是 | 当前有效的 token。 |
| **`userid`** | String | 是 | 用户的唯一性标识。 |

**注意：**`userid`来源于用户发起会话时，调用各端 SDK 的初始化代码时传递给网易七鱼系统的参数中。

**备注：**平台电商中的用户信息对接参数将增加`shopid`,获取对应商户下的用户信息。

#### 响应

接口应该返回一段包含用户数据的 JSON。

```javascript
{
    "rlt": 0,
    "data": [
        {"index": 0, "key": "account", "label": "账号", "value": "zhangsan", "href": "url"},
        {"index": 1, "key": "name", "label": "姓名", "value": "土豪", "edit": true, "map": "real_name", "href": "url"},
        {"index": 2, "key": "phone", "label": "手机", "value": "13800000000", "edit": true, "map": "mobile_phone", "href": "url"},
        {"index": 3, "key": "email", "label": "EMail", "value": "13800000000@163.com", "edit": true, "map": "email", "href": "url"},
        {"index": 4, "key": "vip", "label": "会员", "value": [{"id": 0,"name": "类型一"},{"id": 1,"name": "类型三", "check": true},{"id": 2,"name": "类型二"}], "select": true}
    ],
    "modify_cb": "url"
}
```

| **<div style="width:90px">字段</div>**  | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`rlt`** | Int / String | 是 | 表明接口调用是否成功。值为`0`时表示接口调用成功。关于错误码的详细描述，见Appendix I节。 |
| **`data`** | Array | 是 | 用一个数组表示用户详细信息的数据。 |
| **`modify_cb`** | String | 否 | 如果企业希望客服可以在网易七鱼系统中更新客户的信息，则在此提供一个接口的地址，用于客户信息修改后回调。接口定义见下。 |

其中`data`字段用一个数组描述用户的详细信息，数组中每个元素代表一个数据项。数据项以`<key, value>`对的形式为基础，增加了额外的字段以控制显示样式。数据项定义如下：

| **<div style="width:60px">字段</div>**| **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。特别地，如果该数据可编辑（见`edit`字段说明），`key`将作为请求的内容提交给回调接口。 |
| **`value`** | Mixed | 是 | 该数据显示的值，类型不做限定，根据实际需要进行设定。 |
| **`label`** | String | 是 | 该项数据显示的名称。 |
| **`index`** | Int | 否 | 用于排序，显示数据时数据项按`index`值升序排列；不设定`index`的数据项将排在后面；`index`相同或未设定的数据项将按照其在 JSON 中出现的顺序排列。 |
| **`href`** | String | 否 | 超链接地址。若指定该值，则该项数据将显示为超链接样式，点击后跳转到其值所指定的 URL 地址。 |
| **`edit`** | Boolean | 否 | 表示该项数据是否可编辑，`true`为可编辑，`false`为不可编辑。可编辑时该项数据的值将显示在一个输入框内，内容被编辑后将调用`modify_cb`接口提交更新。 |
| **`map`** | String | 否 | 表明该项数据是否与网易七鱼系统中固定的数据项有映射关系。如真实姓名为`real_name`，手机号为`mobile_phone`，电子邮箱为`email`。如果一个数据项指定了正确的`map`值，则该数据也会同步显示在“当前会话”中右侧用户资料区的“用户资料”标签页下对应的项目中，以及“历史会话”中右侧会话详情区的“用户资料”标签页下对应的项目中。 |
| **`save`** | Boolean | 否 | 是否将姓名、邮箱、手机号三个保留字段数据保存到网易七鱼系统中。`true`为保存，`false`或为空时不保存。将数据保存到网易七鱼系统有助于“历史会话”搜索和列表展示，不保存时则无法通过该项数据进行搜索。仅在轻量接入中不传三个保留字段时有效；若轻量传了，就展示轻量中传得值（轻量传空，就显示空） |
| **`zone`** | Boolean | 否 | 用于表明该项数据信息的展现位置。不设置表示仅显示在呼叫中心和在线会话“更多信息” 的标签下。`true`表示同时可显示在呼叫中心的“账户资料”和在线会话的“用户信息”的页面中。|
| **`select`** | Boolean | 否 | 用于标识该项数据是否为select下拉框。`true`表示该项数据为select下拉框的形式，且当select选项为true时，value字段应该为一个数组的形式。|
| **`check`** | Boolean | 否 | 配合select一起使用，用于标识该select下拉框选项是否被选中。`true`表示该数据项被选中，且check为`true`的选项仅有一个。|

**注意：** 返回的数据将根据预设的样式直接显示在网易七鱼页面相应的位置上。如果数据敏感、保密性要求高，应当在返回时就做**脱敏**处理。网易七鱼系统不会对数据做额外的处理。

<!-- （*示意图，敬请期待*） -->

### 更新用户信息

POST `/[`*`modify_cb`*`]`

用户的唯一性标识和被修改的信息会被作为请求内容 POST 到该接口，与获取信息不一样的是，更新类接口POST提交数据的方式为`application/x-www-form-urlencoded`。

接口地址由获取用户信息接口`/get_user_info`返回的`modify_cb`的内容指定。

#### 请求

```javascript
{
    "appid":"qiyukf",
    "token":"qiyukf_security_token",
    "userid":"zhangsan",
    "data":[
        {"key":"name", "value":"张三"},
        {"key":"phone", "value":"1397777777X"}
    ]
}
```

| **参数** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`appid`** | String | 是 | 企业分配给网易七鱼系统的 appid。 |
| **`token`** | String | 是 | 当前有效的 token。 |
| **`userid`** | String | 是 | 用户的唯一性标识。 |
| **`data`** | Array | 是 | 用一个数组表示修改的用户信息数据。 |

**注意：**`userid`来源于用户发起会话时，调用各端 SDK 的初始化代码时传递给网易七鱼系统的参数中。

其中`data`用一个数组描述提交修改的数据，数组的每个元素表示一个数据项。数据项以`<key, value>`对的形表示：

| **<div style="width:60px">字段</div>**| **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。`key`的值来源于获取用户信息`/get_user_info`接口返回的信息中对应数据项的`key`。 |
| **`value`** | String | 是 | 该数据修改后的值。都视为字符串进行传输。 |

#### 响应

接口应该返回一段包含用户信息更新结果的 JSON。

```javascript
{
    "rlt":0,
    "data":[
        {
            "key":"name",
            "msg":"名字太长了。"
        },
        {
            "key":"phone",
            "msg":"手机号格式错误。"
        }
    ]
}
```

| **<div style="width:60px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`rlt`** | Int / String | 是 | 表明接口调用是否成功。值为`0`时表示接口调用成功。关于错误码的详细描述，见Appendix I节。 |
| **`data`** | Array | 否 | 当`rlt`不为`0`时，用一个数组表示每项数据修改失败的原因提示。 |

其中`data`字段用一个数组描述修改失败的提示信息，数组中每个元素代表一个数据项。数据项定义如下：

| **字段** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。对应请求中数据项的`key`。 |
| **`msg`** | String | 是 | 该项数据修改失败的错误提示。 |

需要注意的是，客服平台前端采用失焦保存策略（鼠标移开后点击其他区域变自动保存），所以每次只能修改一个参数。该策略能为客服减少操作，提升效率。

### 获取订单信息

POST `/get_order` 在线会话获取用户订单信息

POST `/get_call_order` 呼叫中心获取用户订单信息

网易七鱼系统在调用获取访客详细信息接口`/get_user_info`时，会调用该接口。该接口企业在发送请求时，前端会自动发送两遍，第一遍的请求类型是OPTION，第二遍的请求类型是POST，提交数据方式采用`application/json`的方式。该接口可用于电商行业订单类信息，也可以用于金融行业交易类信息。

返回的数据将显示在“当前会话”中右侧用户资料区的“更多信息”标签页下，以及“历史会话”中右侧会话详情区的“更多信息”标签页下。

在这两个标签下，用户的订单信息会显示在用户详细信息（`/get_user_info`接口返回的数据）之下。

用户的唯一性标识会被作为请求内容 POST 到该接口。

#### 请求

```javascript
{
    "appid": "qiyukf",
    "token": "qiyukf_security_token",
    "userid": "zhangsan",
    "count": 10,
    "from": 0,
    "type": 0
}
```

| **参数** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`appid`** | String | 是 | 企业分配给网易七鱼系统的 appid。 |
| **`token`** | String | 是 | 当前有效的 token。 |
| **`userid`** | String | 是 | 用户的唯一性标识。 |
| **`count`** | Int | 是 | 请求返回订单的最大数量。与`from`配合使用实现分页。 |
| **`from`** | Int | 是 | 请求返回订单列表的偏移量，与`count`配合使用实现分页。 |
| **`type`** | Int | 是 | 订单类型 |

**注意：**`userid`来源于用户发起会话时，调用各端 SDK 的初始化代码时传递给网易七鱼系统的参数中。

**备注：**：平台电商中的订单信息对接参数将增加shopid,获取对应商户下的订单信息。

#### 响应

接口应该返回包含指定用户订单类数据列表的一段 JSON。

```javascript
{
    "rlt": 0,
    "count": 2,
    "orders": [
        {
            "index": 0,
            "blocks": [
                {
                    "index": 0,
                    "is_title": true,
                    "data": [
                        { "index": 0, "key": "orderid",  "label": "订单号", "value": "00001", "href": "url"}
                    ]
                },
                {
                    "index": 1,
                    "data": [
                        {"index": 0, "key": "product", "label": "产品名称", "value": "易钱包", "href": "url"},
                        {"index": 1, "key": "amount", "label": "支付金额", "value": "2000.00元"},
			{"index": 2, "key": "basic_proc", "label": "基础产品", "value": "渤海人寿保险" , "href": "url"},
                        {"index": 3, "key": "revenue_yesterday", "label": "昨日收益", "value": "1.00元"},
                        {"index": 4, "key": "revenue_amount", "label": "累计收益", "value": "2.00元" }
                    ]
                }
            ]
        },
        {
	    "index": 1,
	    "blocks": [
                {
                    "index": 0,
                    "is_title": true,
                    "data": [
                        { "index": 0, "key": "orderid",  "label": "订单号", "value": "00002", "href": "url"}
                    ]
                },
                {
                    "index": 1,
                    "data": [
                        {"index": 0, "key": "product", "label": "产品名称", "value": "易钱包", "href": "url"},
                        {"index": 1, "key": "amount", "label": "支付金额", "value": "2000.00元"},
                        {"index": 2, "key": "basic_proc", "label": "基础产品", "value": "渤海人寿保险" , "href": "url"},
                        {"index": 3, "key": "revenue_yesterday", "label": "昨日收益", "value": "1.00元"},
                        {"index": 4, "key": "revenue_amount", "label": "累计收益", "value": "2.00元" }
                    ]
                }
            ]
        }
    ]
}

```

| **<div style="width:70px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`rlt`** | Int / String | 是 | 表明接口调用是否成功。值为`0`时表示接口调用成功。关于错误码的详细描述，见Appendix I节。 |
| **`count`** | Int | 是 | 表示该用户订单类数据的条目总数，而非此次请求返回的订单数量。用于控制分页。 |
| **`orders`** | Array | 是 | 用一个数组表示用户订单列表的数据。 |

订单列表有多层数据嵌套：

* `orders`的每一个元素表示一个订单；
* 每个订单的信息可以按照内容分类，分割成多个区块进行显示，订单的`blocks`数组里保存了这些区块；
* 每个区块中用一个`data`数组保存具体的数据，与用户详细信息数据类似，数组中每个元素代表一个数据项，数据项以`<key, value>`对的形式为基础，增加了额外的字段以控制显示样式。

以下为`orders`中每个订单的数据定义：

| **<div style="width:70px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`index`** | Int | 是 | 用于排序，显示订单列表时订单按`index`值升序排列；不设定`index`的订单将排在后面；`index`相同或未设定的订单将按照其在 JSON 中出现的顺序排列。 |
| **`blocks`** | Array | 是 | 用一个数组表示该订单包含的区块。 |

以下为`blocks`中每个区块的数据定义：

| **<div style="width:60px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`index`** | Int | 是 | 用于排序，显示订单时区块按`index`值升序排列；不设定`index`的区块将排在后面；`index`相同或未设定的区块将按照其在 JSON 中出现的顺序排列。 |
| **`data`** | Array | 是 | 用一个数组表示该区块包含的数据。 |
| **`is_title`** | Boolean | 是 | 表示该区块是否作为该订单的标题行，`true`表示是标题行，`false`表示不是标题行。当一个区块是标题行时，它将会以订单标题的样式进行显示；而且点击该区块时，该订单会有展开/收起表现，被指定为`is_title`的项，它的data数据项的返回数组仅能有一个元素作为订单标题显示。每个blocks中都需要指定一个区块作为标题。 |

以下为`data`中每个数据项的定义：

| **<div style="width:60px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。 |
| **`value`** | Mixed | 是 | 该数据显示的值，类型不做限定，根据实际需要进行设定。 |
| **`label`** | String | 是 | 该项数据显示的名称。 |
| **`index`** | Int | 否 | 用于排序，显示数据时数据项按`index`值升序排列；不设定`index`的数据项将排在后面；`index`相同或未设定的数据项将按照其在 JSON 中出现的顺序排列。 |
| **`href`** | String | 否 | 超链接地址。若指定该值，则该项数据将显示为超链接样式，点击后跳转到其值所指定的 URL 地址。 |
| **`edit`** | Boolean | 否 | 表示该项数据是否可编辑，`true`为可编辑，`false`为不可编辑。可编辑时该项数据的值将显示在一个输入框内，内容被编辑后将调用`modify_cb`接口提交更新。 |
| **`select`** | Boolean | 否 | 用于标识该项数据是否为select下拉框。`true`表示该项数据为select下拉框的形式，且当select选项为true时，value字段应该为一个数组的形式。|
| **`check`** | Boolean | 否 | 配合select一起使用，用于标识该select下拉框选项是否被选中。`true`表示该数据项被选中，且check为`true`的选项仅有一个。|

**注意：** 返回的数据将根据预设的样式直接显示在网易七鱼页面相应的位置上。如果数据敏感、保密性要求高，应当在返回时就做**脱敏**处理。网易七鱼系统不会对数据做额外的处理。

（*示意图，敬请期待*）
### 更新订单信息
POST `/[`*`modify_cb`*`]`

订单用户的唯一标识和被修改的信息会被作为请求内容 POST 到该接口，与获取信息不一样的是，更新类接口POST提交数据的方式为`application/x-www-form-urlencoded`。

接口地址由获取订单信息接口`get_order`返回的`modify_cb`的内容指定。

#### 请求

```javascript
{
    "appid":"qiyukf",
    "token":"qiyukf_security_token",
    "userid":"zhangsan",
    "data":[
        {"key":"amount", "value":"3000元"},
        {"key":"phone", "value":"1397777777X"}
    ]
}
```


| **参数** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`appid`** | String | 是 | 企业分配给网易七鱼系统的 appid。 |
| **`token`** | String | 是 | 当前有效的 token。 |
| **`userid`** | String | 是 | 用户的唯一性标识。 |
| **`data`** | Array | 是 | 用一个数组表示修改的用户信息数据。 |

**注意：**`userid`来源于用户发起会话时，调用各端 SDK 的初始化代码时传递给网易七鱼系统的参数中。

其中`data`用一个数组描述提交修改的数据，数组的每个元素表示一个数据项。数据项以`<key, value>`对的形表示：

| **<div style="width:60px">字段</div>**| **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。`key`的值来源于获取订单信息`/get_order`接口返回的信息中对应数据项的`key`。 |
| **`value`** | String | 是 | 该数据修改后的值。都视为字符串进行传输。 |

#### 响应
接口应该返回一段包含用户信息更新结果的 JSON。

```javascript
{
    "rlt":0,
    "data":[
        {
            "key":"name",
            "msg":"名字太长了。"
        },
        {
            "key":"phone",
            "msg":"手机号格式错误。"
        }
    ]
}
```

| **<div style="width:60px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`rlt`** | Int / String | 是 | 表明接口调用是否成功。值为`0`时表示接口调用成功。关于错误码的详细描述，见Appendix I节。 |
| **`data`** | Array | 否 | 当`rlt`不为`0`时，用一个数组表示每项数据修改失败的原因提示。 |

其中`data`字段用一个数组描述修改失败的提示信息，数组中每个元素代表一个数据项。数据项定义如下：

| **字段** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。对应请求中数据项的`key`。 |
| **`msg`** | String | 是 | 该项数据修改失败的错误提示。 |
### 自定义接口参数
网易七鱼系统提供自定义配置获取用户信息和获取订单信息接口参数的功能。针对会话的场景，网易七鱼提供了在访客接入时采用扩展crm接入的方式自定义配置接口crm请求的参数。

自定义参数的扩展crm接入代码：

```javascript

	ysf.config({
	   uid:"1442286211167",
	   data:JSON.stringify([
		   {"key":"real_name", "value":"土豪"},
		   {"key":"mobile_phone", "hidden":true},
		   {"key":"email", "value":"13800000000@163.com"},
		   {"index":0, "key":"account", "label":"账号", "value":"zhangsan" , "href":"http://example.domain/user/zhangsan"},
		   {"index":1, "key":"sex", "label":"性别", "value":"先生"},
		   {"index":5, "key":"reg_date", "label":"注册日期", "value":"2015-11-16"},
		   {"index":6, "key":"last_login", "label":"上次登录时间", "value":"2015-12-22 15:38:54"},
		   {"type": "crm_param", "key": "startTime", "value": "2017-05-20", "area": "user"},
		   {"type": "crm_param", "key": "endTime", "value": "2017-05-21", "area": "order"},
		   {"type": "crm_param", "key": "creatTime", "value": "2017-05-20"}
	   ])
	});

```

| **字段** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`type`** | String | 是 | 向七鱼声明配置类型为自定义参数类型 |
| **`key`** | String | 是 | 规定请求接口时，自定义参数的参数名字 |
| **`value`** | String | 是 | 规定请求接口时，自定义参数的参数值 |
| **`area`** | String | 否 | 规定自定义参数请求接口的影响范围 |

其中`area`值为`user`时，表示只应用在获取用户信息接口，值为`order`时，表示只应用在获取订单信息接口，不设置`area`会分别应用在获取用户信息和订单的两个接口。

### 获取验证表单

网易七鱼系统提供客服验证访客身份的功能。客服在需要确认访客身份时，发起身份验证请求，访客端即可显示预设的表单。访客填写表单后提交验证，而填写的信息并不会被客服看到。客服将直接收到访客身份验证的结果。

验证访客身份的功能在很多场景下非常必要，比如：

* 访客未登录的情况下，根据关键信息可以确认访客的身份；
* 在涉及资金信息、账户操作等安全性要求高的场景，根据关键信息可以确认访客身份。

身份验证的接口分两部分：

* 获取验证表单的接口；
* 和一系列（也可以合并为一个）验证用户身份的接口。

POST `/get_verify_form`

获取验证表单的接口，用于企业告诉网易七鱼系统哪些信息可以用于访客身份验证，以及验证的接口地址是什么。

#### 请求

```javascript
{
    "appid": "qiyukf",
    "token": "qiyukf_security_token"
}
```

| **参数** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`appid`** | String | 是 | 企业分配给网易七鱼系统的 appid。 |
| **`token`** | String | 是 | 当前有效的 token。 |

#### 响应

接口应该返回包含所有可供客服使用的验证表单的列表。

```javascript
{
    "rlt":0,
    "forms":[
        {
            "index":0,
            "form_name":"verify_id",
            "caption":"请填写以下信息，以便继续为您服务",
            "data":[
                {
                    "index":0,
                    "key":"id_no",
                    "label":"证件号"
                },
                {
                    "index":1,
                    "key":"bank_acc",
                    "label":"银行卡号",
                    "hidden":true
                }
            ],
            "verify_cb":"url",
            "tip":"请准确填写您注册时使用的证件号码，和您账户绑定的银行卡号。这些信息将通过安全连接进行传输，自动验证，不会被任何人看到。"
        },
        {
            "index":1,
            "form_name":"verify_mobile",
            "caption":"表单的标题",
            "data":[
                {
                    "key":"mobile",
                    "label":"手机号"
                }
            ],
            "verify_cb":"url",
            "tip":"描述表单填写规则，或其他说明"
        }
    ]
}
```

| **<div style="width:70px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`rlt`** | Int / String | 是 | 表明接口调用是否成功。值为`0`时表示接口调用成功。关于错误码的详细描述，见Appendix I节。 |
| **`forms`** | Array | 是 | 用一个数组表示客服可以发起的验证表单列表。 |

其中`forms`字段用一个数组表示客服可以发起的验证表单的列表，数组中每一项元素描述一个表单，每个表单的数据项定义如下：

| **<div style="width:80px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`form_name`** | String | 是 | 表单的名称，用于区别不同的表单。当表单数据被提交验证时，`form_name`会同数据一起提交，以便验证接口确认数据来源于哪个表单。特别是当不同表单的`verify_cb`验证地址相同时，`form_name`格外重要。 |
| **`data`** | Array | 是 | 用一个数组表示该表单包含的数据。 |
| **`verify_cb`** | String | 是 | 此表单的验证接口地址 |
| **`index`** | Int | 否 | 用于排序，客服端显示验证表单列表时按`index`值升序排列；不设定`index`的表单将排在后面；`index`相同或未设定的表单将按照其在 JSON 中出现的顺序排列。 |
| **`caption`** | String | 否 | 验证表单在访客段显示时的标题。若不设定，则使用默认标题。 |
| **`tip`** | String | 否 | 设定表单的说明或填写规则，会显示在表单的下方。 |

其中`data`字段用一个数组描述表单所验证的数据项，每个数据项的定义如下：

| **<div style="width:70px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。 |
| **`label`** | String | 是 | 该项数据显示的名称。 |
| **`index`** | Int | 否 | 用于排序，显示表单时数据项按`index`值升序排列；不设定`index`的数据项将排在后面；`index`相同或未设定的数据项将按照其在 JSON 中出现的顺序排列。 |
| **`hidden`** | Boolean | 否 | 表示该项数据是否为密码域，`true`表示是密码域，`false`表示不是密码域。当一项数据为密码域时，用户输入的内容会被`*`或`·`代替。不设定`hidden`时数据项默认不是密码域。 |

<!-- （*示意图，敬请期待*） -->

### 验证用户身份

POST `[``verify_cb``]`

访客填写完身份验证表单，并提交数据后，网易七鱼系统会将用户填写的内容，提交给企业提供的验证用户身份接口进行身份验证。

验证用户身份接口的地址，由获取验证表单接口`/get_verify_form`返回的`verify_cb`的内容指定。

#### 请求

```javascript
{
    "appid":"qiyukf",
    "token":"qiyukf_security_token",
    "form_name":"verify_id",
    "userid":"zhangsan",
    "data":[
        {
            "key":"id_no",
            "value":"330108000000009999"
        },
        {
            "key":"bank_acc",
            "value":"628888888888888"
        }
    ]
}
```

| **参数** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`appid`** | String | 是 | 企业分配给网易七鱼系统的 appid。 |
| **`token`** | String | 是 | 当前有效的 token。 |
| **`form_name`** | String | 是 | 表单的名称，来源于 `/get_verify_form`接口返回的`form_name`字段，由客服推送给访客进行身份验证的表单决定。 |
| **`userid`** | String | 否 | 用户的唯一性标识。 |
| **`data`** | Array | 是 | 用一个数组表示提交验证的数据。 |

其中`data`用一个数组包含提交验证的数据，数组的每个元素表示一个数据项，数据项定义如下：

| **字段** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。来源于`/get_verify_form`接口返回的表单中数据项的`key`字段。 |
| **`value`** | String | 是 | 该数据的值，是访客输入的内容。都视为字符串进行传输。 |

**注意：**`userid`来源于用户发起会话时，调用各端 SDK 的初始化代码时传递给网易七鱼系统的参数中。一般来讲，用户只有在企业应用中登录后，才有这个`userid`。也就是说，在用户未登录状态下，客服发起访客身份验证，验证请求中可能没有`userid`这个字段。验证接口需要充分考虑到这种情况，以确定在验证访客身份时是否会参考`userid`的值。但不论是否会用到，网易七鱼系统总会尽量在请求中带上`userid`信息。

#### 响应

接口应该返回访客身份验证的结果，可以同时返回访客更多的信息。

```javascript
{
    "rlt":0,
    "verify_rlt":true,
    "userid":"zhangsan",
    "data":[
        {"index":0, "key":"name", "label":"姓名", "value":"张三"},
        {"index":1, "key":"real_name_auth", "label":"实名认证", "value":"是"},
        {"index":2, "key":"bank_acc", "label":"绑定银行卡", "value":"************4174"},
        {"index":3, "key":"cash", "label":"余额", "value":"￥8*,***.**"}
    ]
}
```

| **<div style="width:80px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`rlt`** | Int / String | 是 | 表明接口调用是否成功。值为`0`时表示接口调用成功。关于错误码的详细描述，见Appendix I节。 |
| **`verify_rlt`** | Boolean | 是 | 表明访客填写的信息是否通过了验证，`true`表示通过，`false`表示不通过。这个结果会在客服端当前会话内容中显示，提醒客服。 |
| **`userid`** | String | 否 | 当`verify_rlt`为`true`时，可以返回该用户对应的`userid`。若返回`userid`，且企业提供了获取用户信息`/get_user_info`接口、获取订单信息`/get_order`接口，则网易七鱼系统在收到此次响应时会以此`userid`作为请求参数，调用这些接口，并将获取到的用户信息展示在当前会话右侧用户资料区的“更多信息”标签页下。 |
| **`data`** | Array | 否 | 当`verify_rlt`为`true`时，可以返回该用户的一些信息，这些信息将直接在客服端当前会话内容中显示。用一个数组表示这些信息所包含的数据。 |

其中`data`字段用一个数组描述用户的信息，数组中每个元素代表一个数据项。数据项的定义与获取用户信息`/get_user_info`接口返回的`data`定义一致，但不包含`edit`、`map`字段:

| **<div style="width:80px">字段</div>** | **类型** | **必须** | **说明** |
| -- | -- | :--: | -- |
| **`key`** | String | 是 | 数据项的名称，用于区别不同的数据。 |
| **`value`** | Mixed | 是 | 该数据显示的值，类型不做限定，根据实际需要进行设定。 |
| **`label`** | String | 是 | 该项数据显示的名称。 |
| **`index`** | Int | 否 | 用于排序，显示数据时数据项按`index`值升序排列；不设定`index`的数据项将排在后面；`index`相同或未设定的数据项将按照其在 JSON 中出现的顺序排列。 |

<!-- （*示意图，敬请期待*） -->

## 对接方式对比与建议

轻量对接与接口对接的比较。场景、开发难度、数据隔离。

建议不同的企业、团队，用不同的对接方式。

**轻量对接**方式与**接口对接**方式，都是为了给客服在服务过程中提供丰富的访客信息，从而提高服务质量与工作效率。然而两种对接方式在使用场景与实现方式方面各有不同，以下对两种对接方式进行对比。

| **<div style="width:80px">对比角度</div>** | **轻量对接** | **接口对接** |
| :-- | :-- | :-- |
| **数据来源** | 发起会话时，客户端通过 SDK 提交。 | 企业提供的 HTTP 接口。 |
| **调用方式** | 客户端通过 SDK 将数据提交给网易七鱼系统服务器，客服工作台向网易七鱼服务器发起请求。| 客服工作台直接向企业提供的 HTTP 接口发起请求。|
| **数据存储** | 访客数据加密后保存在网易七鱼系统。| 客服工作台前端直接发起请求，数据不经网易七鱼服务器，完全隔离。|
| **功能** | 展现自定义访客信息：√  展现订单类数据：×  数据与企业 CRM 同步：×  访客信息验证：× | 展现自定义访客信息：√  展现订单类数据：√  数据与企业 CRM 同步：√  访客信息验证：√ |
| **展现位置** | 当前会话和历史会话的“用户资料”标签下    。| 当前会话和历史会话的“更多信息”标签下。|
| **开发难度** | 低。  只要客户端有访客的相关数据，就可以按约定拼出 JSON 提交。| 中。  需要开发一套 HTTP 接口，与企业 CRM 数据库对接。若已有类似接口，则非常容易进行对接。|
| **开发成本** | 低。  可快速实现。  无特别要求。| 中。  需要一定的人力进行开发。  需支持**跨域**。|
| **适用场景** | 产品规模**较小**；  产品发展**初期**；  团队规模**较小**；  技术人力**紧张**；  企业 CRM **尚不完善**；  访客数据要求**一般**；  **不需要**订单、交易类信息。| 产品规模**中大**；  产品发展**成熟期**；  团队规模**中大**；  技术人力**有一定支持**；  企业 CRM **较完善**；  访客数据要求**高**；  **需要**订单、交易类信息。|

## Appendix&nbsp;I&nbsp;错误码

所有对接接口`rlt`错误码保留值定义如下：

* `0`: 接口返回正确；
* `1`: 用`appid`和`appsecret`作为认证方式时，表示认证不通过；
* `2`: 用`appid`和`token`作为认证方式时，表示 token 过期；

其他情况，可根据企业业务逻辑自行设定错误码，仅供显示用。

有关认证方式的说明，请参考**接口认证方式**章节。

所有错误码，包括保留的错误码，均可使用整形或字符串。即：

```json
{
    "rlt": 0
}
```

```json
{
    "rlt": "0"
}
```

两种表示方法是等价的。

当接口返回的`rlt`字段值不为`0`时，可以在返回的内容中设置一个`msg`字段，提供用于显示的错误信息。

## 客户中心数据导入

该接口是针对呼叫中心的用户数据设计的，所以phone参数为必须且真实有效的数据。调用该接口需要使用appkey和appsecret，该参数在“设置-信息对接-开发者ID”中获取。首次进入信息对接页面时，开发者ID中只有appkey，appsecret项只有一个“重置”按钮，点击之后会第一次生产appsecret，之后再次点击就是刷新appsecret。
*开发者ID信息一般用于消息接口，只有专业版及以上才有此模块。

### 调用说明

- 本接口只支持POST请求；
- 本接口请求Content-Type类型为：application/json;charset=utf-8；
- 本接口返回类型为JSON，同时进行UTF-8编码。

### 数据校验

七鱼服务器和您的服务器进行数据传递时，POST请求会带以下这些参数：


|参数	|参数说明|
|:----|:----|
| appKey | 你的企业的appKey (仅在您的服务器向七鱼服务器发送数据时需要，七鱼服务器向您的服务器发送数据时无此参数)|
| time | 当前 UTC 时间戳，从 1970 年 1 月 1 日 0 点 0 分 0 秒开始到现在的秒数|
| checksum | SHA1(appSecret + md5 + time), 三个参数拼接的字符串，进行SHA1哈希计算，转化成16进制字符(String，小写)|
| eventType | 七鱼服务器向开发者服务器推送事件时的事件类型。（开发者服务器向七鱼服务器发送请求时无此参数）|

其中，checksum 用于校验数据的完整性，其计算规则中，AppSecret 可在七鱼管理页面「设置」->「消息接口」页面找到，md5 为整个请求 json 字符串的 md5 哈希值（小写形式），即 md5 = MD5(content).toLowwerCase()，如果是上传文件，则是整个文件内容的 md5，time 就是请求参数中的 time。处于安全性考虑，每个 checksum 的有效期为 5 分钟，请确认发起请求的服务器是与标准时间同步的，比如有NTP服务。

计算 checksum 的 Java 示例代码如下：

```java
 public class QiyuPushCheckSum {

    private static final char[] HEX_DIGITS = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

    public static String encode(String appSecret, String nonce, String time) {
        String content = appSecret + nonce + time;
        try {
            MessageDigest messageDigest = MessageDigest.getInstance("sha1");
            messageDigest.update(content.getBytes());
            return getFormattedText(messageDigest.digest());
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    private static String getFormattedText(byte[] bytes) {
        int len = bytes.length;
        StringBuilder buf = new StringBuilder(len * 2);
        for (int j = 0; j < len; j++) {
            buf.append(HEX_DIGITS[(bytes[j] >> 4) & 0x0f]);
            buf.append(HEX_DIGITS[bytes[j] & 0x0f]);
        }
        return buf.toString();
    }
}
```

> **重要提示:** 本文档中提供的所有接口均面向开发者服务器端调用，用于计算 checksum 的 AppSecret 开发者应妥善保管,可在应用的服务器端存储和使用，但不应存储或传递到客户端，也不应在网页等前端代码中嵌入。

### 接口参数说明

使用该接口可以批量更新客户中心中的客户资料，支持批量新增、修改、删除客户资料。

服务器收到的POST请求如下：

```
POST https://qiyukf.com/openapi/crm/syncCrmInfo?appKey=1064deea1c3624c9ee26d1de5ce8481f&time=1463217187&checksum=2f13932c4b7c6888b12360a85261a11b8b543f64
Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{
   "update":
   [{
      "name":"周明月",
      "phone":"13725698555",
      "email":"mingyue@163.com",
      "address":"河南省开封市祥符区范庄村",
      "firstContactTime":1483009843582,
      "lastContactTime":1483957441234,
      "车牌号":"豫BWD001"
   },{
      "name":"鲁城",
      "phone":"13725698666",
      "email":"lucheng@163.com",
      "address":"河南省开封市祥符区范庄村",
      "firstContactTime":1483009963521,
      "lastContactTime":1483957965821,
      "车牌号":"豫BWD002"
   }],
   "delete":
   [{
      "phone":"13725698001"
   }, {
      "phone":"13725698002"
   }]
}
```

请求参数说明如下：

|参数	| 是否必须 |参数说明|
|:----|:------:|:----|
|update|否|需要更新的客户信息|
|delete|否|需要删除的客户信息|
|phone|是|电话号码，做为客户资料的唯一标识|
|name|是|访客姓名，更新时必须传|
|email|否|访客邮箱|
|firstContactTime|否|首次联系时间，必须为毫秒数|
|lastContactTime|否|末次联系时间，必须为毫秒数|
|车牌号|否|作为自定义扩展字段|

更新JSON参数中必须包含update或delete子元素其一。phone作为客户资料的唯一标识，如果系统中不存在phone对应的客户，则执行新增操作，否则执行更新操作。update中不允许存在两条phone一样的客户资料。在「客户中心记录字段」中配置了的，并且启用、显示的字段可以作为扩展字段，以「字段名称」作为属性名，如“车牌号”参数。若未提前配置并启用，在导入数据后将查看不到对应的扩展字段（因为在调用接口导入过程中，服务端会直接忽略这些数据）。如果扩展字段在「客户中心记录字段」中配置的字段类型是时间控件，则值必须为毫秒数，如firstContactTime，如果字段类型是数字，则值必须为数字。

响应参数中各个 key 意义与分配客服的响应参数相同，另外两个不同的key值意义如下：

|参数 |参数说明|
|:----|:----|
|code|错误码。200表示设置成功。|
|message|错误提示信息|


## 七鱼用户认证机制

### 概要

#### 术语
* 第三方：接入七鱼的企业
* `uid`：第三方的用户唯一标识

#### 简介

第三方接入七鱼后，可以上报`uid`，用以访问或关联用户在第三方的真实业务数据。本文描述了七鱼对`uid`进行身份认证的方案。适用于对uid真实性要求较高的场景。

### 方案

#### 认证过程

1. 第三方提供一个生成随机授权码`authToken`的`授权接口`，`authToken`生成后需要暂存在第三方服务器端，供之后的`uid`校验使用；
2. 申请客服或上报CRM信息请求时，先调用上面接口，获取随机授权码`authToken`，并将其传递给七鱼；
3. 七鱼前端将获取到的`authToken`和`uid`作为参数传递到七鱼服务器端；
4. 七鱼服务器收到请求后，携带`authToken`和`uid`，调用第三方`认证接口`进行校验；
5. 第三方将收到的`authToken`与本地暂存的`authToken`进行比较，看是否一致，并返回校验结果；
6. 如果`authToken`和`uid`校验失败，七鱼服务器会忽略请求，并返回失败信息到前端页面。

#### 实施流程
1. 第三方知会七鱼需开启用户认证，并协调约定开启时间；
2. 第三方在约定的开启之前准备好`认证接口`，将线上接口地址提交给七鱼，并与七鱼做好联调工作；
3. 第三方使用支持的SDK，并按下文中代码示例完成`authToken`的设置工作。

### 授权接口

生成随机授权码`authToken`的接口。该接口可以由第三方自己调用，也可以将其url地址提供给七鱼，由七鱼代理获取。

如果该接口由第三方自己调用，需要第三方自己保证生成的`authToken`在使用时不会过期，如定期将新生成的`authToken`通过七鱼SDK接口函数传递给七鱼。七鱼不关心该接口的定义规则。

如果该接口由七鱼调用，则第三方不需要关心`authToken`的过期问题，但接口必须遵循以下定义规范：

#### 调用说明：

- 接口由第三方提供
- 调用方式为HTTP GET请求；
- 接口认证方式与第三方平台Session认证一致
- 接口返回类型为JSON，同时进行UTF-8编码。

响应：

```json
{"code":200,"token":false}
```

响应字段：

| 字段 | 含义 |
| :-: | :-: |
| code | 响应码，200表示生成`authToken`成功 |
| token | 新生成的`authToken`值 |

### 认证接口

七鱼服务端收到相关请求后，会携带认证信息至第三方认证接口完成认证工作。

#### 接口说明：

- 接口由第三方提供
- 调用方式为HTTP POST请求；
- 请求Content-Type类型为：application/json;charset=utf-8；
- 接口返回类型为JSON，同时进行UTF-8编码。

#### 接口调用鉴权

第三方采用计算`checksum`方式对调用方进行鉴权校验，参数包括两部分，header参数和body参数

Header参数，需要放在Http Request Header中

| 参数名 | 参数说明 |
| :-- | :-- |
| checksum | 请求参数根据`checksum`算法计算出来的值 |
| time | 当前UTC时间戳，从1970年1月1日0点0 分0 秒开始到现在的秒数(String) |

Body参数，需要以字节流方式接收，内容为Json字符串

| 参数名 | 含义 |
| :-- | :-- |
| uid | 用户身份信息 |
| token | 认证用`authToken` |

Body参数对应Json数据格式：

```json
{"uid":"uid-from-user-server","token":"auth-token-from-user-server"}
```

计算`checksum`的java代码举例如下：

```java
 public class QiyuAuthCheckSum {

    private static final char[] HEX_DIGITS = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

    public static String encode(String appSecret, String nonce, String time) {
        String content = appSecret + nonce + time;
        try {
            MessageDigest messageDigest = MessageDigest.getInstance("sha1");
            messageDigest.update(content.getBytes());
            return getFormattedText(messageDigest.digest());
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    private static String getFormattedText(byte[] bytes) {
        int len = bytes.length;
        StringBuilder buf = new StringBuilder(len * 2);
        for (int j = 0; j < len; j++) {
            buf.append(HEX_DIGITS[(bytes[j] >> 4) & 0x0f]);
            buf.append(HEX_DIGITS[bytes[j] & 0x0f]);
        }
        return buf.toString();
    }
}
```

encode函数参数来源说明：

| 参数名 | 含义 |
| :-- | :-- |
| appSecret | 登录第三方在七鱼平台账号的管理员端，在设置-基础设置-App接入页面可以查看到 |
| nonce | 将body参数对应的Json字符串参数进行md5加密后得到的值全部转小写后的值 |
| time | 取自header参数中的time参数值 |

接口响应：

```json
{"code":200,"result":false}
```

响应字段：

| 字段 | 含义 |
| :-: | :-: |
| code | 响应码 |
| result | 认证结果 |

响应码值说明：
- 200，表示接口校验成功，
- 300，表示`checksum`计算错误
- 400，表示token值校验失败

> 后续可按需扩展。

### 版本支持

支持用户认证的SDK版本如下：

| 平台 | 版本 |
| :-: | :-: |
| Web-SDK | >=3.5.0 |
| iOS-SDK | >=3.5.0 |
| Android-SDK | >=3.5.0 |

### 示例代码

#### Web
WEB-SDK提供如下两种方式设置及更新`Token`参数:

1. `ysf.config` 设置 `authToken` 属性
2. 调用 `pollAuthToken` 或 `setAuthToken` 设置 `authToken` 值

##### pollAuthToken(url, options)

###### 接口描述
此接口使用`http`请求主动拉第三方接口更新`token`, 由于网络原因等异常情况会导致`http`请求, 一旦请求失败会执行重试机制，保证`token`的更新, 用户可以自定义重试次数(默认为`4次`)。

1. 第三方自定义获取`token`的`api`的接口
2. 更新防止 `token` 失效

###### 请求说明

```html
 GET http://domain/api/getAuthToken // 获取token的第三方的Api接口
 Content-Type : application/x-www-form-urlencoded;charset=utf-8
```



###### 参数说明

| 字段 | 类型 |  必须 | 说明 |
| --- | --- |  --- |  --- |
| interval  | Number| 是  | 轮询时间间隔  |
| retryTime  | Number| 否  | 重试次数，默认为`4`次  |
| data  | Object | 否 | 请求参数  |
| method  | String | 否 | `GET` |
| onsuccess  | Function | 否 | 成功回调 `code==200`才进入此回调 |
| onerror  | Function | 否 | 失败回调 |

```js
    var url = 'http://domain/api/getAuthToken' //  获取token的第三方的Api接口
    var options = {
        interval : 300000,  // 每隔多少毫秒去更新Token
        data : {},          // 请求参数
        retryTime : 4,      // 失败重试次数
        method : 'GET',     // 默认 GET 请求
        onsuccess : function(result){
            // 可选回调, 第三方业务逻辑
        },
        onerror : function(json){
            // 可选回调, 第三方业务逻辑
        }
    }

    ysf.pollAuthToken(url, options);

```
###### 响应说明

```html
 Content-Type : application/json;charset=utf-8
```
###### 参数说明

| 字段 | 类型 |  说明 |
| --- | --- |  --- |
| code  | Number| 状态码  |
| message  | String | 返回消息说明  |
| result  | Object | {authToken : ''} `authToken`必填 |

##### setAuthToken(token)

###### 接口说明

第三方主动调用此`setAuthToken`接口更新用户鉴权的 `token`


#### iOS

设置 authToken ：

```object-c
[[QYSDK sharedSDK] setAuthToken:@"auth-token-from-user-server"];
```
设置轻量 CRM ：

```object-c
/**
 *  完成回调
 */
typedef void(^QYCompletionWithResultBlock)(BOOL isSuccess);

/**
 *  设置个人信息，带authToken校验。用户帐号登录成功之后，调用此函数
 *
 *  @param userInfo 个人信息
 *  @param block authToken校验结果的回调
 */
- (void)setUserInfo:(QYUserInfo *)userInfo
				authTokenVerificationResultBlock:(QYCompletionWithResultBlock)block;
```


#### Android

```java
// 关联用户信息代码中增加以下字段：
YSFUserInfo userInfo = new YSFUserInfo();
userInfo.userId = "userId";
userInfo.authToken = "auth-token-from-user-server";
if (!Unicorn.setUserInfo(account, new RequestCallback<Void>() {
	@Override
	public void onSuccess(Void param) {
		ToastUtils.show("已切换为" + userInfo.userId, Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onFailed(int code) {
        if (code == 414) {
            ToastUtils.show("鉴权失败");
        } else {
            ToastUtils.show("设置用户资料失败，请重试");
        }
    }

    @Override
    public void onException(Throwable exception) {}
})) {
    ToastUtils.show("用户资料格式不对", Toast.LENGTH_SHORT).show();
}
```

### 注意事项
* `authToken`需在申请客服动作发生时保证可用性；
* 认证过程中`authToken`的产生比较关键，为保证安全性，需要在访客成功登录第三方平台后才能请求`授权接口`获取`authToken`。


## 一触即达对接
本文档为一触即达接入文档，企业根据不同业务模块，使用一触即达功能需要实现对应模块的http查询接口。  
根据企业是否允许匿名访客查询，调用查询接口方式分为匿名访客查询和有名访客查询，匿名访客是指企业未做过CRM信息对接的访客，匿名访客查询是允许所有连接到机器人客服的访客使用该查询接口，有名访客查询是只允许做过CRM信息对接的访客才可使用该查询。  
但查询某些信息时（如查订单等需要用户唯一标识的查询）必须要企业提供访客唯一标识，即企业需通过CRM信息对接提供给七鱼。
### 对接方式
说明：所有接口均需符合七鱼对企业接口的请求格式规范和返回格式规范，其规范见下文。  
目前七鱼调用第三方接口时会加上的鉴权方式有两种，一是token校验（复用CRM信息对接里获取token的接口，若能获取到token则使用该token，获取不到token则不使用，此种需企业提供appId,appSecret和获取token的接口），另一种是checkSum校验，需企业提供appId和appSecret,生成checkSum方式见下文。企业可依据不同模块情况采用任意校验方式，或不使用身份校验。
* 企业接口请求说明

1·获取token接口的请求方式同CRM对接文档里相同；  
2·一触即达的所有接口请求均为POST请求；  
3·一触即达的所有接口请求的Content-Type类型均为：application/x-www-form-urlencoded;charset=utf-8。  
例：
```
POST https://qiyeserver/...  HTTP/1.1
Content-Type:application/x-www-form-urlencoded;charset=utf-8
```
* 企业接口返回说明

返回字段定义：

参数 | 是否必填 | 参数说明
---|---|---
code | 是 |请求的返回码
desc | 否 |描述信息
result | 是 |接口调用结果

1·所有一触即达企业接口的返回类型为JSON，同时进行UTF-8编码；  
2·接口的返回值在result字段里，可以是任何对象，只有code=200时才认为调用成功，并使用result字段里的内容做后续操作。  
例：
```
{
  "code": 200,
  "desc": "success",
  "result": {
    "id": "123456",
    "name": "hello world",
    "address": "浙江省杭州市滨江区长河街道网商路599号网易大厦"
  }
}
```
* 接口调用流程

1·若接口不需请求校验并对所有人开放，则接口只需符合上文所列的请求和返回规范即可。  
2·分配appId和appSecret，企业需在页面：接入设置->信息对接->CRM信息对接处填写给七鱼分配的appId和appSecret用于获取token和生成checkSum校验；  
3·若企业设置了获取token接口，并且七鱼成功获取到token后，会在七鱼调用企业一触即达接口时将token作为参数带上；  
4·七鱼在调用企业接口时，将appSecret，time，nonce三个参数使用sha1哈希算法生成checkSum校验和（同消息接口checksum校验和示例代码），其中timestamp为精确到秒的时间戳，nonce为一串随机生成的字符串,参数放在Http Request Header中传输；  
5·POST请求调用第三方接口，七鱼会带上的请求参数有：userId（String字符串类型），plgCode（String字符串类型），params（Map键值对类型，包含请求所需参数，但如订单列表、航班列表等列表类存在分页展示的接口，params里会带上分页参数，分页参数为currentPage和pageSize，分别代表当前要请求第几页，每页的大小是多少），请求参数放在请求体里。  

* 参数说明

参数 | 是否必填 | 参数说明
---|---|--
appId | 是 |企业分配给七鱼的appId
nonce | 是 |随机字符串（最大长度128个字符）
timestamp | 是 |当前UTC时间戳，精确到秒数(String)
checkSum | 是 |SHA1(appSecret + nonce + timestamp),将三个参数按appSecret、nonce、 timestamp顺序拼接成的字符串计算SHA1哈希值，并转化成16进制的小写字符串作为checkSum
userId | 是 |开发者的应用里的用户ID，通过CRM接入同步给七鱼
plgCode | 是 |七鱼要调用的对应模块名称
params | 是 |调用第三方接口的参数列表
token | 否 |获取到的企业token

对于需要分页展示的接口，如订单列表、航班列表等，参数params里会包含currentPage和pageSize参数，currentPage代表当前七鱼要请求第几页的数据，pageSize代表每页有几条数据。例如对于订单列表，若参数为currentPage=2&pageSize=3，则代表七鱼要请求第2页的订单数据，且每页有3条订单数据，参数对应数据库中offset和limit的转换公式为offset=(currentPage-1)*pageSize，limit=pageSize;

* 例子

*获取用户订单列表*

拉取订单列表时，七鱼调用企业服务器，params包含的分页参数为currentPage=1,pageSize=3，代表拉取第1页，共3个订单数据，result返回值为订单列表的JSON数组。  
返回结果为：

```
{
  "code": 200,
  "desc": "success",
  "result": [
    {
      "date": "1483203661000",
      "zipcode": "310052",
      "address": "浙江省 杭州市滨江区 长河街道 网商路599号网易大厦",
      "price": 600,
      "user_name": "黄小白",
      "num": 4,
      "mobile": "15900000000",
      "goods": [
        {
          "goods_status": 1,
          "goods_name": "1ZARA 女装 加大码飞行员夹克 08073243800",
          "goods_price": 100,
          "goods_id": 101,
          "goods_count": "X1",
          "goods_stock": "SKU",
          "goods_img": "https://img.alicdn.com/imgextra/i2/2228361831/TB2Zo._a5GO.eBjSZFjXXcU9FXa_!!2228361831.jpg"
        }
      ],
      "id": 4,
      "shop_name": "hello服装店",
      "status": 3
    },
    {
      "date": "1483290061000",
      "zipcode": "312052",
      "address": "浙江省 杭州市滨江区 长河街道 网商路599号网易大厦",
      "price": 600,
      "user_name": "黄小白",
      "num": 1,
      "mobile": "15900000000",
      "goods": [
        {
          "goods_status": 1,
          "goods_name": "100年传世珐琅锅",
          "goods_price": 600,
          "goods_id": 111,
          "goods_count": "X1",
          "goods_stock": "SKU",
          "goods_img": "https://img.alicdn.com/imgextra/i2/2228361831/TB2Zo._a5GO.eBjSZFjXXcU9FXa_!!2228361831.jpg"
        }
      ],
      "id": 2,
      "shop_name": "珐琅店",
      "status": 2
    },
    {
      "date": "1483290061000",
      "zipcode": "312052",
      "address": "浙江省 杭州市滨江区 长河街道 网商路599号网易大厦",
      "price": 200,
      "user_name": "黄小白",
      "num": 1,
      "mobile": "15900000000",
      "goods": [
        {
          "goods_status": 3,
          "goods_name": "100年传世珐琅锅",
          "goods_price": 600,
          "goods_id": 111,
          "goods_count": "X2",
          "goods_stock": "SKU",
          "goods_img": "https://img.alicdn.com/imgextra/i2/2228361831/TB2Zo._a5GO.eBjSZFjXXcU9FXa_!!2228361831.jpg"
        }
      ],
      "id": 23,
      "shop_name": "珐琅店",
      "status": 2
    }
  ]
}
```
订单格式说明：

参数 | 类型 | 必须 | 说明
---|---|---|---
id  | String | 是 | 订单号
shop_name | String | 是 | 店铺名称
status | Int | 是 | 订单状态
date | String | 是 | 订单创建时间
user_name | String | 是 | 收货人
address | String | 是 | 收货地址
mobile | String | 否 | 手机号
num | Int | 否 | 订单包含的商品数量
price | Double | 否 | 订单总价格
zipcode | String | 否 | 邮政编码
goods | Array | 是 | 订单包含的商品列表

商品格式说明:

参数 | 类型 | 必须 | 说明
---|---|---|---
goods_id  | String | 是 | 商品编号
goods_price | Double | 是 | 商品价格
goods_name | String | 是 | 商品名称
goods_img | String | 是 | 商品图片url
goods_count | Int | 是 | 商品数量
goods_stock | String | 是 | 商品SKU，颜色、尺码等
goods_status | Int | 是 | 商品售后状态

*返回订单详情*

获取订单详情时，参数module值为detail，params包含orderId订单号参数获得该订单的详情信息。

```
{
  "code": 200,
  "desc": "success",
  "result": {
    "date": "1483290061000",
    "zipcode": "312052",
    "address": "浙江省 杭州市滨江区 长河街道 网商路599号网易大厦",
    "price": 600,
    "user_name": "黄小白",
    "num": 1,
    "mobile": "15900000000",
    "goods": [
      {
        "goods_status": 1,
        "goods_name": "100年传世珐琅锅",
        "goods_price": 600,
        "goods_id": 111,
        "goods_count": "X1",
        "goods_stock": "SKU",
        "goods_img": "https://img.alicdn.com/imgextra/i2/2228361831/TB2Zo._a5GO.eBjSZFjXXcU9FXa_!!2228361831.jpg"
      }
    ],
    "id": 2,
    "shop_name": "珐琅店",
    "status": 2
  }
}
```

### 移动SDK接入指南

* Android 接入指南

1.请确保你的SDK为最新版本<a href="https://bintray.com/qiyukf/maven/qiyu-android-sdk/_latestVersion" target="_blank"><img src="https://api.bintray.com/packages/qiyukf/maven/qiyu-android-sdk/images/download.svg" style="width:auto" height=20 width=104 alt='Download' /></a>

```
compile 'com.qiyukf.unicorn:unicorn:3.10.0'
```

2.在SDK初始化时，为 `YSFOptions` 的 `onBotEventListener` 赋值：

```
// ... your codes
// YSFOptions options = new YSFOptions();
options.onBotEventListener = new OnBotEventListener() {
    @Override
    public boolean onUrlClick(Context context, String url) {
        // 加入处理一触即达url的代码
        BrowserActivity.start(context, url);
        return true;
    }
};
// Unicorn.init(this, "appKey", options, new UnicornImageLoader());
// ... your codes
```

* IOS 接入指南

1.需要的 iOS SDK 版本是 v3.10.0 或以上

2.自定义 bot 点击事件处理：

```objc
QYCustomActionConfig *actionConfig = [[QYSDK sharedSDK] customActionConfig];    
//botClick 是 bot 相关点击回调
actionConfig.botClick =【你的代码】
```

### 微信模板消息接入指南

该接口用于将企业发送给用户的资料，以微信模板消息的形式发出。使用该接口前，需要企业的公众号已添加相应的模板，且企业需要按照指定格式提供访问接口，供七鱼获取对应的数据。
通过该接口，七鱼提供了特定业务支持的数据源集。企业设置的模板中支持哪些数据，将对应数据的在模板中的key填入请求中即可完成设置。当用户查询时，七鱼会访问企业提供数据的接口，然后会将对应的数据根据模板配置填入，最后发给用户。

该接口目前仅支持设置物流类的模板信息。

POST请求为：
```
POST https://qiyukf.com/openapi/settings/wxTemplates?appKey=1064deea1c3624c9ee26d1de5ce8481f&time=1463216914&checksum=e72be4487b6fc03e0f914fc11e4053d771598d93
Content-Type:application/json;charset=utf-8
```

其中各参数说明可参见[消息接口文档](https://qiyukf.com/newdoc/html/message_interface.html#数据校验)

设置物流类模板信息post请求内容字段列举如下：

```json
{
    "appId":"wxcxxxxxxxxx",
    "logistic": {
        "template_id":"s8BlawhJMXO4YWEvvsrBiBrxuBJzYwpk-HchuAa9jbc",
        "top":"first",
        "bottom":"remark",
        "id": "keyword1",
        "name":"keyword2",
        "last_state": "keyword3",
        "update_time": "keyword4",
        "sender":"keyword5",
        "sender_site":"keyword6",
        "sender_phone":"keyword7",
        "receiver":"keyword8",
        "receiver_site":"keyword9",
        "receiver_phone":"keyword10",
        "order_id":"keyword11",
        "order_time":"keyword12",
        "price":"keyword13",
        "status":"keyword14",
        "good_name":"keyword15"
    }
}
```

公共字段说明：

| 字段名 | 是否必须| 说明 |
|:--:|:---:|:-:|
|appId|**是**|公众号的appId，可在公众号后台查看。|
|logistic|否|物流类模板信息，物流类必须，其他类无需设置。|

物流类模板字段说明：

| 字段名 | 是否必须| 说明 |
|:--:|:---:|:-:|
|template_id|**是**|该模板的ID, 可在公众号后台看到|
|top|否|显示在模板消息第一行的内容的key|
|bottom|否|显示在模板消息最后一行的内容的key|
|id|否|物流运单ID的key|
|name|否|快递公司名称的key|
|last_state|否|运单的最新状态|
|update_time|否|最新状态的更新时间|
|sender|否|寄件人的key|
|sender_site|否|寄出地址的key|
|sender_phone|否|寄件人联系电话的key|
|receiver|否|收件人的key|
|receiver_site|否|收件地址的key|
|receiver_phone|否|收件人电话的key|
|order_id|否|与运单关联的商品订单ID的key|
|order_time|否|商品下单时间或者寄件时间的key|
|price|否|商品价格或者快递单价格的key|
|status|否|商品订单状态或者运单状态的key|
|good_name|否|商品名称的key|

示例：
某个公众号添加了ID为 s8BlawhJMXO4YWEvvsrBiBrxuBJzYwpk-HchuAa9jbc 的模板，其格式为：

```
{{first.DATA}}
订单编号：{{keyword1.DATA}}
取件公司：{{keyword2.DATA}}
{{remark.DATA}}
```

调用设置接口的内容如下：

```json
{
    "appId": "wxcxxxxxxx",
    "logistic": {
        "template_id":"s8BlawhJMXO4YWEvvsrBiBrxuBJzYwpk-HchuAa9jbc",
        "top":"first",
        "bottom":"remark",
        "id": "keyword1",
        "name":"keyword2"
    }
}
```


## 呼入事件对接

呼叫中心用户电话呼入的时候，七鱼系统通过该接口向第三方系统获取用户信息，包括访客VIP级别、外接IVR路由、crm信息，根据获取到的信息进行访客分配，及访客信息更新；IVR过程中，`IVR校验`以及导航项动作`自定义IVR接口`和`播放内容接口`也使用该接口对接，通过参数`eventtype`进行区分。

参数`eventtype`类型说明：

|参数值	|参数说明|
|:----|:----|
|1  |获取用户信息|
|2  |IVR校验|
|3  |自定义IVR接口|
|4  |播放内容接口|

七鱼服务器向第三方接口请求信息时，采用密钥安全认证机制，保证第三方系统的信息安全。

### 对接参数说明

#### 调用说明
* 本接口只支持POST请求；

* 本接口请求Content-Type类型为：application/json;charset=utf-8;

* 本接口返回类型为JSON，同时进行UTF-8编码。

curl请求样例如下

1.电话呼入时获取用户信息，包括访客VIP级别、外接IVR路由、crm信息

```
curl -X POST \
  'http://www.xxx.com/api/ivr/crminfo?checksum=f570a5eb049eb803b086e45829b07e48&time=1511832531451' \
  -H 'content-type: application/json;charset=utf-8' \
  -d '{
    "eventtype": 1, 
    "phone": "15854582215"
}'
```

2.IVR校验请求

```
curl -X POST \
  'http://www.xxx.com/api/ivr/crminfo?checksum=f570a5eb049eb803b086e45829b07e48&time=1511832531451' \
  -H 'content-type: application/json;charset=utf-8' \
  -d '{
    "eventtype": 2, 
    "phone": "15854582215",
    "sessionid": 216629286,
    "ivrid": 3545,
    "keyrecord": "2-1-0",
    "input": "110226198501272116"
}'
```

3.IVR过程中`自定义IVR接口`事件通知

```
curl -X POST \
  'http://www.xxx.com/api/ivr/crminfo?checksum=f570a5eb049eb803b086e45829b07e48&time=1511832531451' \
  -H 'content-type: application/json;charset=utf-8' \
  -d '{
    "eventtype": 3, 
    "phone": "15854582215",
    "sessionid": 216629286,
    "ivrid": 3545,
    "keyrecord": "1-3"
}'
```

4.IVR过程中`播放内容接口`请求播放内容

```
curl -X POST \
  'http://www.xxx.com/api/ivr/crminfo?checksum=f570a5eb049eb803b086e45829b07e48&time=1511832531451' \
  -H 'content-type: application/json;charset=utf-8' \
  -d '{
    "eventtype": 4, 
    "phone": "15854582215",
    "sessionid": 216629286,
    "ivrid": 3545,
    "keyrecord": "1-2"
}'
```

#### 请求参数和数据校验
七鱼服务器向第三方服务器请求用户信息时，url携带参数checksum和time。

|参数	|参数说明|
|:----|:----|
| time | 当前 UTC 时间戳，从 1970 年 1 月 1 日 0 点 0 分 0 秒开始到现在的秒数|
| checksum | SHA1(appSecret + md5 + time)， 三个参数拼接的字符串，进行SHA1哈希计算，转化成16进制字符(String，小写)|

POST请求body参数为jsonStr格式，例如，当eventtype=2时，POST请求参数样例如下：

```json
{
    "eventtype": 2, 
    "phone": "15854582215",
    "sessionid": 216629286,
    "ivrid": 3545,
    "keyrecord": "2-1-0",
    "input": "110226198501272116"
}
```

|参数	|参数说明|
|:----|:----|
| eventtype | 请求事件类型，1为请求用户信息 |
| phone | 用户电话号码 |
| sessionid | 呼叫通话ID，请选择使用 |
| ivrid | IVR模型ID，请选择使用 |
| keyrecord | IVR事件对应按键顺序 |
| input | IVR校验时用户输入值 |

以上`checksum` 用于校验数据的完整性，其计算规则中，`appSecret` 可在七鱼管理页面「设置」->「消息接口」页面找到，`md5` 为Body参数jsonStr字符串的 md5 哈希值（小写形式），即 md5 = MD5(jsonStr).toLowwerCase()，`time` 就是请求参数中的 time。处于安全性考虑，每个 `checksum` 的有效期为 5 分钟，请确认发起请求的服务器是与标准时间同步的，比如有NTP服务。

具体计算 `checksum` 的 Java 示例代码请参考本页「客户中心数据导入」->「数据校验」中的示例代码

### 接口返回数据说明
接口返回数据为JSON格式，最外层参数如下：

|参数	|参数说明|
|:----|:----|
| code | 错误码。200表示设置成功。 |
| result | code为200时，返回值有效。eventtype=1时返回访客信息，为JSON格式数据；eventtype=4时返回播放内容，为String格式数据 |
| message | 请求错误时，填错误提示信息 |

当eventtype=1时`result` 数据格式如下

|参数	|是否必须|数据类型|参数说明|
|:----|:----|:----|:----|
| name | 否 | String | 访客姓名 |
| crm | 否 | String | 访客CRM信息，格式为JsonString |
| level | 否 | Int | 访客VIP级别，值为0到10 |
| groupId | 否 | Int | IVR分流客服组ID |
| staffId | 否 | Int | IVR分流客服ID |

* `crm`数据格式参照本页的「轻量CRM对接」->「Web SDK调用」中的`data`参数。
* 非VIP用户`level`等于0，VIP级别等级为1到10数值
* `groupId`和`staffId`值对应七鱼系统中的客服组ID和客服ID，从七鱼管理系统「设置」->「高级设置」->「访客分配」->「ID查询」中查找获取。如果设置了`staffId`，七鱼会优先查找`staffId`对应的呼叫客服，如果该客服正好空闲的话，电话将优先分配给他。否则系统判断是否有返回`groupId`值，如果有`groupId`值，系统再优先查找`groupId`对应的呼叫客服组进行分配。如果没有返回`staffId`和`groupId`，或者返回的值不存在，七鱼将执行一般IVR分流。

返回的完整参数格式如下：

```json
{
    "code": 200, 
    "message": "",
    "result": {
        "name": "王贵",
        "crm": JSON.stringify([
            {"key":"real_name", "value":"土豪"},
            {"key":"mobile_phone", "hidden":true},
            {"key":"email", "value":"13800000000@163.com"},
            {"index":0, "key":"account", "label":"账号", "value":"zhangsan" , "href":"http://example.domain/user/zhangsan"},
            {"index":1, "key":"sex", "label":"性别", "value":"先生"},
            {"index":5, "key":"reg_date", "label":"注册日期", "value":"2015-11-16"},
            {"index":6, "key":"last_login", "label":"上次登录时间", "value":"2015-12-22 15:38:54"}
        ]),
        "level": 0,
        "groupId": 415451,
        "staffId": 2164623
    }
}
```

### 第三方接口样例
以`SpringMVC`框架为例，第三方系统提供的接口如下：

```java
@RequestMapping(value = "/crminfo", method = RequestMethod.POST)
@ResponseBody
public String getClientCrmInfoByPhone(
        @RequestParam(value = "checksum", required = true) String checksum,
        @RequestParam(value = "time", required = true) String time,
        @RequestBody(required = true) String jsonStr
) {
    // 此处省略业务代码
}
```
