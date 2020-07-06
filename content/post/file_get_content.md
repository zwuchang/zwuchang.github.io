+++
title='使用file_get_contents发送post请求'
tags=['php']
categories=['php']
date="2017-11-21T17:16:29+08:00"
toc=true
+++

使用file_get_contents发送post请求
<!--more-->

```php
<?php
$requestBody = '{"username":"josh"}';

$context = stream_context_create(
    array(
        'http'  =>  array(
            'method'    =>  'POST',
            'header'    =>  'Content-Type:application/json;charset=utf-8;\r\n' .
                            'Content-Length: ' . mb_strlen($requestBody),
            'content'   =>  $requestBody
            ,
        )
    )
);

$response = file_get_contents($url,false,$context);
```
