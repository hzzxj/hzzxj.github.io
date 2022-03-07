---
title: web安全相关-安全测试
date: 2021-05-24 10:13:37
tags:
- web
- security
categories:
- security
---


### 记一次安全测试记录

#### 敏感数据加密存储
  ##### 配置文件敏感数据加密存储
  SpringBoot使用jasypt加解密密码。
  ```
  <!--jasypt-->
    <dependency>
        <groupId>com.github.ulisesbocchio</groupId>
        <artifactId>jasypt-spring-boot-starter</artifactId>
        <version>2.0.0</version>
    </dependency>
  ```
  配置文件
  ```
  # 加密的密钥
  jasypt:
    encryptor:
      password: Hzzxj
  
  # 格式：ENC()
  spring.datasource.username=ENC(EROfZyZxd9F0CYgarXQGtLGFKGzOyD+k)
  spring.datasource.password=ENC(X02b/D0sOY81kkSV7v4g2/tLw8Ynq6wQ)
  ```

  ```
  # 获取密文
  
  ```

