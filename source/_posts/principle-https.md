---
title: https 原理和应用
date: 2020-01-14 20:44:26
tags: web
---

## Principles and Applications of https

# 一. 背景

* 理解http 和https 的区别
* 理解http 1.0 和http 1.1 和http2.0 的区别
* 在对外提供接口的情况下，如何设计和保证数据的安全可靠性
<!--more-->
# 二. 原理
* [www.ichenpeng.net](http://www.ichenpeng.net/1176592)
# 三. 应用
```java
package com.codewindy.mongodb.utils;

import cn.hutool.core.date.DateUtil;
import cn.hutool.core.map.MapUtil;
import cn.hutool.crypto.SecureUtil;
import cn.hutool.crypto.asymmetric.KeyType;
import cn.hutool.crypto.asymmetric.RSA;
import cn.hutool.crypto.asymmetric.Sign;
import cn.hutool.crypto.asymmetric.SignAlgorithm;
import cn.hutool.http.HttpRequest;
import org.apache.commons.lang3.RandomStringUtils;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;

import java.util.HashMap;

/**
 * @author codewindy
 * @date 2020-01-14 8:59 PM
 * @since 1.0.0
 * https://www.bookstack.cn/read/hutool/093507f34fe0715d.md
 */
public class TestAESByHutool {
    public static void main(String[] args) {
        //1. 生成公钥和私钥并指定加密type 和长度

        RSA rsa = new RSA();
        String privateKeyBase64 = rsa.getPrivateKeyBase64();
        String publicKeyBase64 = rsa.getPublicKeyBase64();
        System.out.println("keyPair.getPrivate() 私钥= " + privateKeyBase64);
        System.out.println("keyPair.getPublic() 公钥= " + publicKeyBase64);

        //2. 获取待加密的敏感原始数据
        String content = HttpRequest
                .get("https://api.myip.com/")
                //超时，毫秒
                .timeout(20000)
                .header(HttpHeaders.ACCEPT, MediaType.APPLICATION_JSON_VALUE)
                .execute()
                .body();
        //3. 每次生成AES 16 位随机密码
        String aesKey = RandomStringUtils.randomNumeric(16);
        System.out.println("每次生成AES 16 位随机密码aesKey = " + aesKey);

        //4. 使用公钥加密 aes对称密码 后的key
        String encryptAesKey = rsa.encryptBase64(aesKey, KeyType.PrivateKey);
        System.out.println("使用公钥加密 aes对称密码 后的encryptAesKey = " + encryptAesKey);
        //5. AES 加密后的内容
        String encryptContent = SecureUtil.aes(aesKey.getBytes()).encryptBase64(content);
        System.out.println("AES 加密后的内容 encryptContent = " + encryptContent);

        Sign sign = SecureUtil.sign(SignAlgorithm.MD5withRSA);
        byte[] signedContent = sign.sign(content.getBytes());
        System.err.println("对原始数据进行签名signedContent = " + signedContent);


        // 模拟获取request对象里面的parameters
        String urlPath = "/admin/queryUserInfo";
        String httpMethod = "POST";
        long clientTimeStamp = DateUtil.currentSeconds();
        HashMap<String, Object> requestParams = MapUtil.newHashMap();
        requestParams.put("urlPath", urlPath);
        requestParams.put("httpMethod", httpMethod);
//        requestParams.put("clientSign", request.getClientSign());
        requestParams.put("clientTimeStamp", clientTimeStamp + "");
        String sign2String = MapUtil.sortJoin(MapUtil.sort(requestParams), "&", "=", true);

        String calculateSign = SecureUtil.hmacSha1(aesKey.getBytes()).digestHex(sign2String);
//        calculateSign计算出来的sign和request 请求里面的参数clientSign进行比较，如果不一致说明数据被篡改了s

        System.out.println("计算出来的sign和request 请求里面的参数clientSign 进行比较 = " + calculateSign);
        System.out.println("解密-----------------------------------------------------");
        //6. 使用私钥解密后的key

        String decryptAesKey = rsa.decryptStr(encryptAesKey, KeyType.PublicKey);
        System.out.println("使用私钥解密后的decryptAesKey = " + decryptAesKey);
        //7. 解密得到明文的 aes 对称密码 解密加密的content数据

        String decryptContent = SecureUtil.aes(decryptAesKey.getBytes()).decryptStr(encryptContent);
        System.out.println("解密得到明文的 aes 对称密码 解密加密的content数据 = " + decryptContent);
        //8. 签名和验签 https://hutool.cn/docs/#/crypto/%E7%AD%BE%E5%90%8D%E5%92%8C%E9%AA%8C%E8%AF%81-Sign

        boolean verify = sign.verify(content.getBytes(), signedContent);
        System.err.println("验签状态verify = " + verify);
    }

}

```

# 四. 参考
* [https://www.kancloud.cn/ichenpeng/blog/1176592](https://www.kancloud.cn/ichenpeng/blog/1176592)
* [countdownLatch 使用](https://www.kancloud.cn/ichenpeng/blog/1094601)
* [Hutool doc](https://hutool.cn/docs/#/)