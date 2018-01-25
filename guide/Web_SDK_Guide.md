# 网易七鱼Web开发指南


## WEB-SDK概要

七鱼客服SDK可以帮助第三方网站及应用快速构建一个完善的客服系统生态圈，SDK提供较为完善的客服应用功能，及简洁的API接口。

SDK提供如下扩展功能：

* 自定义接入
* CRM对接
* 访客分流
* 商品链接
* 未读消息
* VIP接入支持
* 常见问题设置
* 网站访问统计
* 多机器人接入

以下所有接入代码的例子都可以在这里下载。[接入Demo](./res/webDemo.zip)


## Web接入方案

### 基本接入配置

1. 管理端获取接入代码
> 管理端-设置-网站接入-接入及开关-接入代码。

2. 完整页面实例展示

```html

	<!DOCTYPE html>
	<html>
	    <head>
	        <title>在线客服</title>
	        <meta charset="utf-8"/>
	    </head>
	    <body>
	        <script src="https://qiyukf.com/script/044865c94981c048609d5c94c1ae9c6d.js" charset="UTF-8"></script>
	        <!-- YOUR CODE HERE -->
	    </body>
	</html>

```
3. 管理端可以实现如下入口配置：    

    1. 三种访客端入口挂件模式
    2. 三种访客端对话框打开方式
    3. 自定义配置多个访客端对话框模板，包括颜色、欢迎语等；如需配置每个链接打开的对话框，参考“open接口-打开聊天窗口”一节
    4. 自定义配置多个会话邀请模板，包括底图、接入会话触发动作等，示例如下：

```html

	<!DOCTYPE html>
	<html>
	    <head>
	        <title>自定义配置会话邀请模板</title>
	        <meta charset="utf-8"/>
	    </head>
	    <body>
	        <script src="https://qiyukf.com/script/044865c94981c048609d5c94c1ae9c6d.js?templateId=123" charset="UTF-8"></script>
	        <!-- templateId表示自定义会话邀请的模板标识 -->
	        <!-- YOUR CODE HERE -->
	    </body>
	</html>

```
如需会话邀请模板关联某个对话框样式模板，请使用会话邀请模板的高级自定义功能，将“接受邀请”的操作设置指向某个对话框模板。



### SDk初始化

1. 七鱼web sdk会在加载完成后自动进行初始化，可以通过监听onload事件来捕获初始化完成的通知（建议在sdk初始化完成后再进行设置用户信息、设置商品信息等后续操作，否则部分设置可能无法生效）

2. 如果在sdk初始化完成之前对onload事件进行监听，绑定的回调函数会等待sdk初始化完成后执行；如果在sdk初始化完成后进行监听，则回调函数会立即执行

示例代码：

```javascript
	ysf.on({
	    'onload': function(){
	        ysf.config({
	            uid:"1442286211167",
	            name:'test',
	            email:'test@163.com',
	            mobile:'13888888888'
	        });
	    }
	})

```

## 企业CRM对接
企业可以通过调用config接口配置企业用户信息，以便在客服系统中将会话与企业产品中的用户关联起来，如以下方式


```html
 <script src="https://qiyukf.com/script/[APPKEY].js" charset="UTF-8"></script>

 <script>
    ysf.config({
       uid:"1442286211167",  // 用户Id
       name:'test',          // 用户名称
       email:'test@163.com', // 用户邮箱
       mobile:'13888888888'  // 用户电话
   });
 </script>

```

> ysf.config支持传入成功和失败回调函数（success、error）。当在ios的h5页面使用七鱼sdk时，设置用户信息是一个异步的过程；如果需要保证用户信息设置成功，则应该在成功回调中进行后续操作（如进行页面跳转、打开聊天窗口等）

示例代码：

```javascript
    ysf.config({
        uid:"1442286211167",
        name:'test',
        email:'test@163.com',
        mobile:'13888888888',
        success: function(){     // 成功回调
			ysf.open();
    	},
    	error: function(){       // 错误回调
    		// handle error
    	}
    });

```

### 企业用户账号的切换
企业对接的情况下， 企业需要切换账号时， 接入七鱼的第三应用需要调用 `ysf.logoff()` 函数登出。


### 企业基础信息

使用SDK时可以配置一些企业产品相关的用户信息，以便在客服后台可以正确识别出对应的用户，七鱼客服WEB SDK提供企业可以设置以下信息：

| 参数    | 类型   | 描述  |
| :----: | :----: | :---- |
| uid    | String | 可选，用户在企业产品中的标识，便于后续客服系统中查看该用户在产品中的相关信息，不传表示匿名用户 |
| name   | String | 可选，用户在企业产品中的名称，便于后续客服系统中查看该用户在产品中的相关信息 |
| email  | String | 可选，用户在企业产品中的邮箱，便于后续客服系统中查看该用户在产品中的相关信息 |
| mobile | String | 可选，用户在企业产品中的手机，便于后续客服系统中查看该用户在产品中的相关信息 |
| data   | String | 可选，用户在企业中的其他详细信息，JSON字符串，不可以通过脚本参数输入，只能通过 [ysf.config](config接口) 接口配置 |

### 企业扩展信息

扩展信息字段data用一个数组的 [JSON](http://www.json.org/) 字符串描述用户的详细信息，数组中每个元素代表一个数据项。数据项以(key, value)对的形式为基础，增加了额外的字段以控制显示样式。数据项定义如下：

| 字段    |   类型 |    必须 |    说明 |
| :----: | :----: | :---- | :---- |
| key    | String  | 是 | 数据项的名称，用于区别不同的数据。其中real_name、mobile_phone、email为保留字段，分别对应客服工作台用户信息中的“姓名”、“手机”、“邮箱”这三项数据。保留关键字对应的数据项中，index、label属性将无效，其显示顺序及名称由网易七鱼系统指定。 |
| value  | Mixed   | 是 | 该数据显示的值，类型不做限定，根据实际需要进行设定。 |
| label  | String  | 是 | 该项数据显示的名称。 |
| index  | Int     | 否 | 用于排序，显示数据时数据项按index值升序排列；不设定index的数据项将排在后面；index相同或未设定的数据项将按照其在 JSON 中出现的顺序排列。 |
| href   | String  | 否 | 超链接地址。若指定该值，则该项数据将显示为超链接样式，点击后跳转到其值所指定的 URL 地址。 |
| hidden | Boolean | 否 | 仅对mobile_phone、email两个保留字段有效，表示是否隐藏对应的数据项，true为隐藏，false为不隐藏。若不指定，默认为false不隐藏。 |

### 轻量CRM接入方案

接入代码：

```javascript

    ysf.config({
        uid:"1442286211167",
        name:'test',
        email:'test@163.com',
        mobile:'13888888888'
    });

```

### 扩展CRM接入方案

接入代码:

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
		   {"index":6, "key":"last_login", "label":"上次登录时间", "value":"2015-12-22 15:38:54"}
	   ])
	});

```


### 接口CRM对接

企业 CRM 接口由客户企业提供并实现，网易七鱼系统将在必要的时候调用特定的接口，并将接口返回的数据以约定的样式显示在客服工作界面。 特别要注意企业需要进行 **服务器跨域配置**， 具体详情见: [企业信息对接](http://qiyukf.com/newdoc/html/qiyu_crm_interface.html)




## 商品链接

发起会话前，企业可以将用户正在查看的商品信息发送给客服，以便客服在后台及时预判访客需要咨询的内容。

```javascript

    ysf.product({
        show : 1, // 1为打开， 其他参数为隐藏（包括非零元素）
        title : '标题',
        desc : '商品描述',
        picture : '商品图片地址',
        note : '备注',
        url : '跳转链接'
    })

```
> ysf.product支持传入成功和失败回调函数（success、error）。当在ios的h5页面使用七鱼sdk时，设置商品链接是一个异步的过程；如果需要保证商品链接设置成功，则应该在成功回调中进行后续操作（如进行页面跳转、打开聊天窗口等）

示例代码：

```javascript
	ysf.product({
	    show : 1, // 1为打开， 其他参数为隐藏（包括非零元素）
	    title : '标题',
	    desc : '商品描述',
	    picture : '商品图片地址',
	    note : '备注',
	    url : '跳转链接',
        success: function(){     // 成功回调
			ysf.open();
    	},
    	error: function(){       // 错误回调
    		// handle error
    	}
	})

```

> 注： show -参数用于控制访客端商品链接的是否显示；对于客服端，只要接入就一定会显示商品链接信息


## 访客分流

网易七鱼客服系统默认会按照智能方式分配客服, 如果想让访客分配给指定的客服组或者客服，则需要使用访客分流功能

### 如何查找客服信息

在管理端 -> 设置 -> 访客分配 -> ID查询下面, 可以查找到相应的客服id和客服组id。
在管理端 -> 设置 -> 访客分配 -> 高级自定义分配（WEB端）-> Web端自定义链接分配下，可以直接复制链接地址接入

接入代码：

```javascript

    ysf.config({
        uid:"1442286211167",
        name:'test',
        email:'test@163.com',
        mobile:'13888888888',
        staffid:'123', // 客服id
        groupid: '123' // 客服组id
    });

```

### 机器人优先开关
在3.1版本中，增加了robotShuntSwitch参数，该参数取值为0或1，在指定了访客分流时有效；如果robotShuntSwitch为1，访客会先由机器人接待，当转人工咨询时再分配给设置的分流客服

接入代码：

```javascript

    ysf.config({
        uid:"1442286211167",
        name:'test',
        email:'test@163.com',
        mobile:'13888888888',
        staffid:'123',
        robotShuntSwitch: 1 // 机器人优先开关
    });

```

## 访客端自定义

七鱼客服的系统默认入口是放在页面右下角。 如果企业需要自定义入口， 可以进行如下操作步骤：
1. 管理中心 - 接入设置 - 网站接入 - 访客开关， 设置 `停用`
2. 企业开发者配置对话框样式模板
3. 在入口的点击事件时调用 WEB-SDK 的 `ysf.open()` API接口

## 未读消息

企业需要对未读消息进行些额外的操作， 七鱼暴露 **unread** 事件读取未读消息,具体使用如下:

```javascript

    ysf.on({
        unread : function(msg){
            if(msg.total){
                // 处理逻辑
            }
        }

    });

```

## VIP接入支持

企业需要对不同级别的访客进行接待区分为VIP客户和普通客户；七鱼支持VIP客服的接入，VIP可区分等级，上限为10级。

接入代码

```javascript
	ysf.config({
		uid : 'user',
		level : 1 // vip级别
	})
```

## 常见问题

访客进入咨询时，如果进入了机器人会话，企业可以通过设置常见问题，将一些用户可能需要问的问题首先发送给用户。通过高级自定义配置，可以给不同页面中的不同咨询入口设置不同的常见问题。

### 查询问题模板id

在管理端 -> 设置 -> 机器人 -> 常见问题设置 -> 设置常见问题模板，可以查找到企业的常见问题模板及其id。

接入代码

```javascript
    ysf.config({
       uid:"201609051809",
       name:'接入',
       email:'test@163.com',
       mobile:'13888888888',
       qtype: 1056         //常见问题模板id
   });

```


## 网站访问统计

七鱼SDK在接入网站提供服务的同时，可以收集访客的访问信息（例如PV，UV，着陆页，客户来源，搜索关键词，跳出率等数据），并在管理端 工作报表>访问统计 中显示。
### 使用方法

确保您网站的需要统计的每个独立页面都接入了七鱼SDK，七鱼SDK会自动获取上述信息。（如部分页面不需要客服咨询入口，可以在接入SDK时传入一个hidden=1隐藏）。

## 多机器人接入

对于开通了多机器人的企业，对于不指定robotId的访客入口，网易七鱼系统会在机器人开关打开的情况下自动匹配当前的默认机器人，如果想让指定的机器人服务访客，则需要使用多机器人接入功能指定robotId。

### 如何查找指定的机器人信息

开通了多个机器人的企业，在管理端 -> 设置 -> 机器人列表中找到对应的机器人ID。
在管理端 -> 访客分配 -> 高级自定义分配（WEB端）-> Web端自定义链接分配下，可以直接复制链接地址接入

接入代码：

     ysf.config({
        uid:"usr18398492039",
        name:'mixRobotTestUser',
        email:'test@163.com',
        mobile:'13888888888',
        robotId: '12345'  // 机器人ID
    });

## 接口说明

### API接口列表

|接口名称| 接口说明|
|:---|:---|
|ysf.on| 事件监听接口，用于`onload`, `unread`功能接口|
|ysf.config| 用户信息配置接口|
|ysf.product| 商品链接接口|
|ysf.logoff| 用户登出需要调用的接口|
|ysf.open| 打开客服聊天窗口|
|ysf.url| 获取打开聊天窗口的URL地址

###	config&nbsp;接口

配置企业及用户信息，嵌入七鱼客服SDK后可直接配置这些信息

输入参数说明：

| 参数    | 类型   | 描述  |
| :----: | :----: | :---- |
| options | Object | 企业配置信息 |
| options.uid | String | 企业当前登录用户标识，不传表示匿名用户 |
| options.name | String | 企业当前登录用户名称 |
| options.email | String | 企业当前登录用户标识，不传表示匿名用户 |
| options.mobile | String | 企业当前登录用户手机号 |
| options.data | String | 企业当前登录用户其他信息，JSON字符串 |
| options.staffid | String | 指定客服id |
| options.groupid | String | 指定客服组id |
| options.robotShuntSwitch | Number | 机器人优先开关（访客分配） |
| options.level | Number | 企业当前登录用户vip等级 |
| options.qtype | Number | 企业常见问题模板id |
| options.success | Function | 配置成功回调 |
| options.error | Function | 配置失败回调 |

使用范例

```javascript
ysf.config({
    uid:"1442286211167",
    name:'test',
    email:'test@163.com',
    mobile:'13888888888',
    data:JSON.stringify([
       {"key":"real_name", "value":"土豪"},
       {"key":"mobile_phone", "hidden":true},
       {"key":"email", "value":"13800000000@163.com"},
       {"index":0, "key":"account", "label":"账号", "value":"zhangsan" , "href":"http://example.domain/user/zhangsan"},
       {"index":1, "key":"sex", "label":"性别", "value":"先生"},
       {"index":5, "key":"reg_date", "label":"注册日期", "value":"2015-11-16"},
       {"index":6, "key":"last_login", "label":"上次登录时间", "value":"2015-12-22 15:38:54"}
    ]),
    staffid:'123',
    groupid: '123',
    level: 1,
    qtype: 1056,
    success: function(){
    	// todo
    },
    error: function(){
    	// handle error
    }
});

```

### open&nbsp;接口

打开客服聊天窗口

在引入七鱼SDK时会自动应用企业后台设置的接入配置，打开聊天窗口时直接调用open接口即可，聊天窗口会以后台设置的模式打开。或者通过传入templateId应用自定义配置的对话框样式。

输入参数说明：

| 参数    | 类型   | 描述  |
| :----: | :----: | :---- |
| options | Object | 企业配置信息 |
| options.templateId | Number | 对话框样式模板标识 |

使用范例

```javascript
ysf.open({
	templateId:123
});
```

###  url&nbsp;接口

通过此接口可获取聊天窗口的URL地址，如果想在当前窗口打开聊天窗口可以用以下方式

```html

<a href="#" id="online-service">在线客服</a>

<script src="https://qiyukf.com/script/044865c94981c048609d5c94c1ae9c6d.js" charset="UTF-8"></script>
<script>
    document.getElementById('online-service').onclick = function(){
        location.href = ysf.url();
    };
</script>

```


## FAQ

### ysf未定义的错误

Q: console中有如下错误,我应该如何处理?

```
	Uncaught ReferenceError:ysf is not defined
```
A: 执行 *ysf.config*, *ysf.open* 函数的代码需要放在ysf脚本加载之后


### 用户信息设置不生效的问题

Q: 在ios的h5页面使用七鱼sdk，设置的用户信息无法生效

A: 请先检查自己的接入代码，是否在设置用户信息（调用ysf.config）后立即打开了聊天窗口（或进行了页面跳转），这种情况下用户信息很可能设置失败。
当在ios的h5页面使用七鱼sdk时，设置用户信息是一个异步的过程；如果需要保证用户信息设置成功，则应该在成功回调中打开聊天窗口或进行后续操作

示例代码：

```javascript
    ysf.config({
        uid:"1442286211167",
        name:'test',
        email:'test@163.com',
        mobile:'13888888888',
        success: function(){     // 成功回调
			ysf.open();
    	},
    	error: function(){       // 错误回调
    		// handle error
    	}
    });

```

### 浮层模式问题
Q: 手机上是否支持浮层样式？

A：PC网页可以支持浮层、弹窗、标签，目前手机浏览器只能支持新标签样式

