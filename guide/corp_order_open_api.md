# 企业成单数据对接接口

## 场景及用途
为帮助企业客服管理者更好地考核客服绩效，统计客服的销售及访客订单的转化等情况，七鱼系统OpenAPI提供企业成单数据对接接口，企业可将其成单数据加密同步至七鱼系统，同步成功后，七鱼系统根据成单数据计算出该订单是由那个客服的服务产生的，从而生成企业客服的订单绩效。

## 数据同步流程
数据同步采取批量同步，企业一次最大可上传1000个订单数据，订单数据传输采用JSON格式。
具体步骤如下：
1. 企业将订单数据按规定格式组装成json数组对象。
2. 对组装过的json数组对象使用[AES算法](https://zh.wikipedia.org/wiki/%E9%AB%98%E7%BA%A7%E5%8A%A0%E5%AF%86%E6%A0%87%E5%87%86)加密。
3. 对加密后的文本做checksum校验，具体见接口鉴权部分。
4. 使用https请求开放接口。

## 接口鉴权

参阅[《消息接口文档》](https://qiyukf.com/newdoc/html/message_interface.html#数据校验)

## 订单属性

订单属性 | 描述 | 是否必须
---|---|---
orderId | 企业数据库中标识订单唯一的项，比如订单表主键或者订单号，要求在企业数据库中必须保证全局唯一,在1~255字符之间 | 是
uid | 标识该订单是那个客户所下的单，要求必须能唯一标识出该下单客户,在1~128字符之间 | 是
orderTime | 该订单的下单时间，精确到毫秒，比如下单时间是2016-11-11 11:11:11，则下单时间是1478833871000,下单时间必须大于0 | 是
payTime | 该订单的支付时间，精确到毫秒，同orderTime；目前不支持配置，默认按订单的支付时间来算客服的订单绩效 ，支付时间必须大于0 | 是
amount | 该订单中商品的件数 | 否
price | 该订单的总金额 | 否

## 示例
比如某企业订单信息如下：

orderId | uid | orderTime | payTime | amount | price
---|---|------|---|---|---
orderId1 | uid1 | 1478826671000 | 1478833871000 | 1 | 100.00 |
orderId2 | uid2 | 1478826881888 | 1478833888888 | 2 | 888.88 |
1. 组装成的json数组对象如下：

```
[
    {
        "uid":"uid1",
        "amount":1,
        "orderTime":1478826671000,
        "orderId":"orderId1",
        "payTime":1478833871000,
        "price":100
    },
    {
        "uid":"uid2",
        "amount":2,
        "orderTime":1478826881888,
        "orderId":"orderId2",
        "payTime":1478833888888,
        "price":213
    }
]
```
2. 将组装后的json数组对象进行AES加密，加密代码如下：

```
/**
     * 使用AES算法对content加密
     *
     * @param content    待加密的内容
     * @param encryptKey 加密密钥
     * @return 加密后的byte[]
     */
    public static String encrypt(String content, String encryptKey) {
        try {
            KeyGenerator kgen = KeyGenerator.getInstance("AES");
            SecureRandom random = SecureRandom.getInstance("SHA1PRNG");
            random.setSeed(encryptKey.getBytes());
            kgen.init(128, random);
            Cipher cipher = Cipher.getInstance("AES");
            cipher.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(kgen.generateKey().getEncoded(), "AES"));
            return byteArr2HexStr(cipher.doFinal(content.getBytes("utf-8")));
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
    /**
     * 将byte数组转换为16进制值的字符串
     *
     * @param b 需要转换的byte数组
     * @return 转换后的字符串
     */
    private static String byteArr2HexStr(byte[] b) {
        int length = b.length;
        StringBuffer sb = new StringBuffer(length * 2);
        for (int i = 0; i < length; i++) {
            int temp = b[i];
            while (temp < 0) {
                temp = temp + 256;
            }
            if (temp < 16) {
                sb.append("0");
            }
            sb.append(Integer.toString(temp, 16));
        }
        return sb.toString();
    }
```
加密密钥使用企业的AppSecret加密，假设某企业AppSecret为123456，则加密示例如下：

加密前:
```

[{"uid":"uid1","amount":1,"orderTime":1478826671000,"orderId":"orderId1","payTime":1478833871000,"price":100.00},{"uid":"uid2","amount":2,"orderTime":1478826881888,"orderId":"orderId2","payTime":1478833888888,"price":213}]
```

加密后:
```
11896bd6f9940b859746a40eace4cbccac23c6208bd7b93fc706afdfc65d707313618b12a1ee4baf18c9b73de825c169f0af355e3f85fc02aad4fc69c6027cc4a750789eb71274d3e49bbad65db483d1cba9e9dd5fb674cd02d1019ad31f6a4912326a639f1f2471708c88dbed0c3b01639ebf0ca6cf3734b26076a06a65615b98ffc188994261f2531d539f5b5c71742672fbc288fd608121866e493a178efe6ee880df386e03b03995b6da43c93429781d011f5459c67eefc88fa6c23e07f2f8337adbefce92f49316b90e9dfa4df4766e9e43488d5875bad81e0804713a34
```

由于使用https传输及AES算法加密内容，双重保证传输数据的安全性。

## 上传订单信息接口

POST 请求为：
```
POST https://qiyukf.com/openapi/order/upload?appKey=[APP_KEY]&time=[TIME]&checksum=[CHECKSUM]

Content-Type:application/json;charset=utf-8
```

上传成功响应:

```
{
    "code":200,
    "message":"upload order info success, total = 2"
}
```
* 响应码为200时，message里total内容为上传成功的订单个数。

## 错误码

| **参数** | **参数说明** |
| :-----: | :----- |
| 200 | 成功 |
| 14001 | App Key错误 |
| 14002 | 请求校验失败 |
| 14003 | 时间错误 |
| 14004 | 请求参数错误 |
| 14300 | 解码错误，可能因为请求之后重置了AppSecret |
| 14500 | 内部错误 |