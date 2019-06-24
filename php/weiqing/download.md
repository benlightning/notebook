```php

if (PHP_SAPI == 'cli'){
    die('This example should only be run from a Web Browser');
}


global $_GPC,$_W;

$list = pdo_fetchall("SELECT * FROM " . tablename('table') . " WHERE condition  ORDER BY id DESC ");

$tableheader = array('序列号', '微信昵称', '微信OpenID', '时间');

$html = "";

foreach ($tableheader as $value) {

  $html .= $value . ",";

}

$html .= "\r\n";

foreach ($list as $value) {

  $html .= $value['id'] . ",";

  $html .= $value['nickname'] . ","; 

  $html .= $value['openid'] . ","; 

  $html .= date('Y-m-d H:i:s', $value['createtime']) . "\r\n";  

}



header("Content-type:text/csv");

header("Content-Disposition:attachment;filename=下载文件.csv");

header('Cache-Control:must-revalidate,post-check=0,pre-check=0');   

header('Expires:0');   

header('Pragma:public');  

$html = iconv('utf-8','gb2312//IGNORE',$html);

echo $html;

exit();
```