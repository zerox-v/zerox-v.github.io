---
title: LayUI Table 添加禁止选中
date: 2019-05-22 14:16:35
tags: 
- LayUI
- Table
categories:
- LayUI
---
LayUI这几年比较流行,里面的Table组件也比较强大，但是前面的CheckBox没有禁止选中功能，今天就来试试，看看能不能给添加一个禁止选中功能。
## Fork LayUI源码
- [LayUI项目地址](https://github.com/sentsin/layui)
- Clone到本地
- 找到src里面lay下面的modules文件夹里面的table.js

## 添加字段标记
- 参照这里我们也添加一个"IS_DISABLE"的标记
![2019-05-22-16-14-50](http://pruksi5mp.bkt.clouddn.com/2019-05-22-16-14-50.png)

- 添加完如下
```javascript
 config: {
            checkName: 'LAY_CHECKED', //是否选中状态的字段名
            indexName: 'LAY_TABLE_INDEX', //下标索引名
            disableName: 'LAY_DISABLED' //是否禁用
        } //全局配置项
```
## 修改代码逻辑

- 找到区域模板里面的  ` lay-filter="layTableAllChoose" `
![2019-05-22-14-55-15](http://pruksi5mp.bkt.clouddn.com/2019-05-22-14-55-15.png)

- 在后面添加如下代码
``` javascrtpt
 {{# if(item2[d.data.disableName]){ }} disabled {{# }; }} 
 ```
 - 添加列渲染
 ![2019-05-22-16-02-15](http://pruksi5mp.bkt.clouddn.com/2019-05-22-16-02-15.png)

 - 修改选择逻辑
 ![2019-05-22-16-12-51](http://pruksi5mp.bkt.clouddn.com/2019-05-22-16-12-51.png)
 ![2019-05-22-16-13-23](http://pruksi5mp.bkt.clouddn.com/2019-05-22-16-13-23.png)
- [详细修改请点这里](https://github.com/ZeroX-V/layui/commit/a9b20f55c9881916125b9e9e62a1c33b4804a351?diff=unified)
 

## 测试

 - 在数据里面添加 `"LAY_DISABLED": true,`
 - 来看看效果
![1234](http://pruksi5mp.bkt.clouddn.com/1234.gif)

## 源码
 - [点这里看源码](https://github.com/ZeroX-V/layui/blob/master/src/lay/modules/table.js)