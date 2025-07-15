---
title: OpenSSL生成证书
date: 2024-07-21 10:23
tags: 
- openssl
categories:
- openssl
---
准备openssl配置文件req.conf
``` bash
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
prompt = no
[req_distinguished_name]
C = {国家}
ST = {省}
L = {市}
O = {组织}
OU = {组织单元}
CN = {证书主体唯一标识}
[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1 = {可能使用的SAN-1:IP/域名}
DNS.2 = {可能使用的SAN-2:IP/域名}
DNS.3 = {可能使用的SAN-3:IP/域名}
```

生成Certificate Signing Request（CSR，证书签名请求）以及私钥
```bash
openssl req -new -out company_san.csr -newkey rsa:2048 -nodes -sha256 -keyout company_san.key.temp -config req.conf
```
可以通过命令验证生成的证书请求是否符合预期

```bash
openssl req -text -noout -verify -in company_san.csr
```

将PKCS#8格式的私钥文件company_san.key.temp转换成RSA格式

```bash
openssl rsa -in company_san.key.temp -out company_san.key
```

生成带有SAN扩展字段的证书文件

```bash
openssl x509 -req -days 999999 -in company_san.csr -signkey company_san.key -out company_san.crt -extensions v3_req -extfile req.conf
```

将私钥和证书一起打包成PKCS#12格式，用pfx或者p12作为后缀即可

```bash
openssl pkcs12 -export -out company_san.p12 -inkey company_san.key -in company_san.crt  -password pass:123456
```
利用 keytool 将pfx格式证书转换成jks格式，方便jdbc连接

```bash
keytool -importkeystore -srckeystore company_san.p12 -srcstoretype PKCS12 -srcstorepass 123456 -destkeystore company_san.jks -deststoretype JKS -deststorepass 123456 -destkeypass 123456
```