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

```php
//导出csv
$tableheader = array('序列号', '姓名', '手机号');

$html = "";

foreach ($tableheader as $value) {

  $html .= $value . ",";

}

$html .= "\r\n";
$list = array();
foreach ($list as $value) {

  $html .= $value['id'] . ",";

  $html .= $value['realname'] . ",";  

  $html .= $value['phone'] . ","; 

}



header("Content-type:text/csv");

header("Content-Disposition:attachment;filename=全部用户列表.csv");

header('Cache-Control:must-revalidate,post-check=0,pre-check=0');   

header('Expires:0');   

header('Pragma:public');  

$html = iconv('utf-8','gb2312//IGNORE',$html);

echo $html;

exit();
```