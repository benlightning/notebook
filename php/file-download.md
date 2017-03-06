# 下载文件

```php
if ($return['code'] == 0) {
    $url = $return['url']; //获取图片地址
    ob_start();
    readfile($url);
    $img = ob_get_contents();
    ob_end_clean();
    $size = strlen($img);
    header("Content-type: application/octet-stream");
    header("Accept-Ranges: bytes");
    header("Accept-Length: " . $size);
    header("Content-Disposition: attachment; filename=" . $name . ".jpg");
    echo $img;
    exit;
}
```