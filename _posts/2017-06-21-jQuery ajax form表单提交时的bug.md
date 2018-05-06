---
layout: default
title: jQuery ajax form表单提交时的bug
---

# {{ page.title }}
使用jquery-form插件进行ajax提交表单时，如果表单中有file类型的控件且没有选择文件时，提交的数据将会把这个字段当普通空字符串处理，也就是说，提交的信息里不含filename等信息，这会导致某些server端代码出错，例如SpringMVC的MultipartFile类型字段接收上传数据的情况。

报错信息类似：
> [Failed to convert property value of type 'java.lang.String' to required type 'org.springframework.web.multipart.MultipartFile' for property 'uploadImage'; nested exception is java.lang.IllegalStateException: Cannot convert value of type [java.lang.String] to required type [org.springframework.web.multipart.MultipartFile] for property 'uploadImage': no matching editors or conversion strategy found]
还有：
> org.springframework.beans.BeanInstantiationException: Could not instantiate bean class [org.springframework.web.multipart.MultipartFile]: Specified class is an interface
>	at org.springframework.beans.BeanUtils.instantiateClass(BeanUtils.java:101)

解决方法是修改jquery.form.js，对于有file控件或enctype="multipart/form-data"的form都采用iframe方式提交。

原代码：
`var shouldUseFrame = (hasFileInputs || multipart) && !fileAPI;`
修改为：
`var shouldUseFrame = (hasFileInputs || multipart);`
即可。
