# docsify
网页文档

# 目录

- [目录](#目录)
- [下单](#下单)
- [签名加密规则](#签名加密规则)

# 下单

| 名称 | 说明 |
| --- | --- |
| 地址 | http://45.120.53.197:20000/api/openapi/order |
| 请求方式 | POST |

| 参数 | 说明 |
| --- | --- |
| code | 通道编码 |
| uuid | 身份号 |
| out_trade_no | 你的订单号 |
| amount | 下单金额，单位元 |
| notice_url | 支付成功后的通知地址 |
| sign | 签名 |

- 响应信息

```json
// success 为 true 表示下单成功
{
    "success": true,
    "data": {
        "url": "支付链接",
    }
}
```

# 签名加密规则

1. 将要发送的数据以字典序排序，空值不参与排序
2. 以 url 键值对格式拼接参数
3. 拼接商户密钥
4. 对拼接后的字符串进行 md5 加密，然后转换为大写

```java
// java 示例
/**
 * 签名算法sign
 * @param parameters 生成签名的参数
 * @param key 商户密钥
 * @return sign 签名
 */
public static String createSign(SortedMap<Object,Object> parameters, String key){
    StringBuffer sb = new StringBuffer();
    //所有参与传参的参数按照accsii排序（升序）
    Set es = parameters.entrySet();
    Iterator it = es.iterator();
    while(it.hasNext()) {
        Map.Entry entry = (Map.Entry)it.next();
        String k = (String)entry.getKey();
        Object v = entry.getValue();
        // 以 url 键值对格式拼接参数
        if(null != v && !"".equals(v) && !"sign".equals(k) && !"key".equals(k)) {
            sb.append(k + "=" + v + "&");
        }
    }
    String substring = sb.toString().substring(0, sb.length() - 1);
    // 拼接商户密钥。直接拼接 key 就好了
    substring += key;
    String sign = MD5Util.MD5Encode(substring, "UTF-8").toUpperCase();
    return sign;
}
```