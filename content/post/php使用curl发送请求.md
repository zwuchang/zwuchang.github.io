+++
title='php中使用curl发送http请求'
tags=['php']
categories=['php']
date="2017-03-14T15:22:27+08:00"
toc=true
url="php_curl_http.html"
+++

php中使用curl发送http请求
<!--more-->

## curl 发送get请求
```php
<?php
/**
 * 使用curl发送get请求
 * @param string $url 请求地址
 * @param string $proxy 代理设置  如http://192.168.2.54:1080
 * @param mixed $post_data 发送数据
 * @return mixed
 */
function curl_get($url,$proxy)
{
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_TIMEOUT, 30);
    if(isset($_SERVER['HTTP_USER_AGENT '])){
        curl_setopt($ch, CURLOPT_USERAGENT, $_SERVER["HTTP_USER_AGENT"]);
    }
    if($proxy){
        curl_setopt ($ch, CURLOPT_USERAGENT, $proxy);
    }
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION,1);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);

    curl_setopt($ch, CURLOPT_IPRESOLVE, CURL_IPRESOLVE_V4 );
    curl_setopt($ch, CURLOPT_HTTPGET, 1);
    curl_setopt($ch, CURLOPT_DNS_USE_GLOBAL_CACHE, false );
    curl_setopt($ch, CURLOPT_DNS_CACHE_TIMEOUT, 2 );


    $out = curl_exec($ch);
    if($out === false){
        echo '<br/>';
        echo curl_error($ch);
        var_dump(curl_getinfo($ch));
    }
    curl_close($ch);
    return $out;
}
```

## curl发送post请求
```php
<?php
/**
 * 使用curl发送post请求
 * @param string $url 请求地址
 * @param mixed $post_data 发送数据
 * @return mixed
 */
function curl_post($url,$post_data){
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL,$url);
    curl_setopt($ch, CURLOPT_TIMEOUT, 30);
    curl_setopt($ch, CURLOPT_USERAGENT, $_SERVER["HTTP_USER_AGENT"]);
    if(!is_array($post_data) && !empty($post_data)){
        curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json; charset=utf-8', 'Content-Length: ' . strlen($post_data)));
    }
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_POSTFIELDS,$post_data);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
    $out = curl_exec ($ch);
    if($out === false){
        echo curl_error($ch);
    }
    curl_close ($ch);
    return $out;
}
```

## 借助curl上传文件
```php
<?php
 function curlImgFormat($imgRealPath=''){
    $postData = array('media'=>new CURLFile($imgRealPath));
    if(version_compare(PHP_VERSION,'5.5') > 0){
        $postData = array('media'=>new CURLFile($imgRealPath));
    }

    return $postData;
}
```
