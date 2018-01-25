# OpenAPI — 工单接口

# 场景及用途

目前工单只能由客服在七鱼客服系统内提交,用户是无法主动提交工单的。工单接口旨在为客户提供一个提交工单到七鱼客服系统的入口。

有了工单接口,客户可以把工单提交的主动权转交给自己的用户。

# 接口鉴权

参阅[《消息接口文档》](https://qiyukf.com/newdoc/html/message_interface.html#数据校验)

# 接口协议

接口属性描述了接口的方法、地址和`Content-Type`，Payload部分描述了接口的协议内容。

## 创建工单

POST 请求为：
```
POST https://qiyukf.com/openapi/worksheet/create?appKey=[APP_KEY]&time=[TIME]&checksum=[CHECKSUM]

Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{
  "title":"工单标题",
  "uid":"someone",
  "typeId":12345,
  "content":"我有一个问题",
  "userName":"游客1",
  "userMobile":"18888888888",
  "userEmail":"some@163.com",
  "targetStaffId":12345,
  "targetGroupId":12345,
  "staffId":12345,
  "priority": 5,
  "attachments":[
    {
      "fileName":"附件1",
      "type":1,
      "payload":"附件BASE64"
    }
  ],
  "properties":[
    {
      "key":"服务器",
      "value":"瘦西湖"
    },
    {
      "key":"玩家ID",
      "value":"12345"
    }
  ]
}
```

接口参数说明如下：

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| title | 是 |工单标题，不超过30个字符 |
| uid | 是 | 开发者的应用里的用户ID，不超过64个字符 |
| typeId | 否 | 分类ID，0表示未分类 |
| content | 是 | 工单内容，不超过3000个字符 |
| userName | 否 | 用户姓名，不超过128个字符 |
| userMobile | 否 | 用户联系方式，不超过128个字符 |
| userEmail | 否 | 用户联系邮箱，不超过255个字符 |
| targetStaffId | 否 | 指定客服ID |
| targetGroupId | 否 | 指定客服分组ID |
| staffId | 是 | 托管客服ID |
| priority | 否 | 优先级,5=一般;8=紧急;10=非常紧急 |
| attachments | 否 | 附件列表 |
| attachments.fileName | 是 | 附件的文件名，不超过128个字符 |
| attachments.type | 是 | 附件的类型，1=文件的Base64，目前仅支持此类型 |
| attachments.payload | 是 | 对应type的内容 |
| properties | 否 | 附加属性对，json格式，不超过1024个字符 |

>
* userMobile和userEmail二者必填其一；
* targetStaffId和targetGroupId二者必填其一；
* 创建一个工单时最多可以添加5个附件，其中二进制格式的附件总大小不得超过5MB。

响应

```json
{
  "code":200,
  "message":12345
}
```
> 响应码为200时，message内容为工单ID

## 用户查看工单

POST 请求为：
```
POST https://qiyukf.com/openapi/worksheet/list?appKey=[APP_KEY]&time=[TIME]&checksum=[CHECKSUM]

Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{
  "uid":"jgg",
  "limit":3,
  "offset":0
}
```

接口参数说明如下：

| 参数 | 是否必须 | 参数说明 |
| :-----: | :-------: | :----- |
| uid | 是 | 开发者的应用里的用户ID，不超过128个字符 |
| limit | 否 | 一次请求获取的工单数上限，最大为100 |
| offset | 否 | 偏移量 |

响应

```json
{
  "code":200,
  "message": [
    {
      "id":12345,
      "title":"工单1",
      "status":5,
      "createtime":"2016-07-04 00:08:00"
    },
    {
      "id":23456,
      "title":"工单2",
      "status":10,
      "createtime":"2016-07-04 00:08:30"
    }
  ]
}
```

> 响应码为200时，message为当前用户的工单列表。其中，id为工单id，title为工单标题，status为工单状态[1]，createtime为工单创建时间。该列表按创建时间逆序排列。

## 查看工单状态

POST 请求为：
```
POST https://qiyukf.com/openapi/worksheet/status?appKey=[APP_KEY]&time=[TIME]&checksum=[CHECKSUM]

Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{
  "sheetId":12345,
  "uid":"someone",
  "history":3,
  "details":true
}
```

接口参数说明如下：

| 参数 | 是否必须 | 参数说明 |
| :----: | :----: | :-- |
| uid | 是 | 开发者的应用里的用户ID，不超过128个字符 |
| sheetId | 是 | 工单ID |
| history | 否 | 1.不返回历史评论，2.返回最新的一条评论，3.返回所有历史评论 |
| details | 是 | 布尔型,是否带上工单信息 |

响应

```json
{
  "code":200,
  "message": {
    "status":10,
    "history":[
      {
        "datetime":"2016-07-04 08:00:00",
        "comment":"comment"
      }
    ],
    "worksheet":{
      "title":"工单标题",
      "content":"工单内容",
      "priority":5,
      "typeId":12345,
      "typeName":"分类名",
      "properties":"",
      "attachments":[
        {
          "name":"附件名",
          "size":1024,
          "url":"http://path.to/it.jpg"
        }
      ]
    }
  }
}
```

worksheet节点为工单详情,字段含义如下:

| 字段 | 含义 |
| :-----: | :------- |
| title | 工单标题 |
| content | 工单内容 |
| priority | 工单优先级,含义请参考创建工单接口 |
| typeId | 工单分类ID |
| typeName | 工单分类名称 |
| properties | 工单附加属性 |
| attachments | 工单附件列表 |
| attachments.name | 工单附件列表 |
| attachments.size | 工单附件大小,单位Byte |
| attachments.url | 工单附件下载地址 |

> 响应码为200时，message即为历史评论，datetime为评论发布时间，comment为评论内容。如果某次历史操作未提交评论，则不返回。

## 获取工单分类

POST 请求为：
```
POST https://qiyukf.com/openapi/category/list?appKey=[APP_KEY]&time=[TIME]&checksum=[CHECKSUM]

Content-Type:application/json;charset=utf-8
```

请求内容示例如下：

```json
{
  "type":2
}
```

接口参数说明如下：

| 参数 | 是否必须 | 参数说明 |
| :-----: | :----- | :--- |
| type | 是 | 分类类型，2:工单分类（暂不支持其他分类类型） |

响应

```json
{
  "code":200,
  "message": {
    "status":10,
    "history":[
      {
        "id":12345,
        "parentId":0,
        "name":"母婴",
        "rank":2
      }
    ]
  }
}
```

响应码为200时，message即为分类列表，id为分类ID，parentId为父类ID，0表示无父分类，name为分类名，rank为同级分类的排序权值。

## 错误码
| 参数 | 参数说明 |
| :-----: | :----- |
| 200 | 成功 |
| 14001 | App Key错误 |
| 14002 | 请求校验失败 |
| 14003 | 时间错误 |
| 14004 | 请求参数错误 |
| 14500 | 内部错误 |
| 14100 | 客服不存在 |
| 14101 | 分类不存在 |
| 14102 | 客服分组不存在 |
| 14103 | 附件个数超出限制 |
| 14104 | 附件大小超出限制 |
| 14105 | 用户不存在 |
| 14106 | 工单不存在 |
| 14107 | 附件大小超出企业总大小限制 |
| 14200 | 不支持的分类类型 |
| 16001 | 服务不可用 |

附录

[1] 工单状态：1:已提交，5：待申领， 10：处理中，20：已完结