## 生成临时二维码代码

```php
load()->model('account');
$acc = WeAccount::create($_W['acid']);
$token = $acc->getAccessToken();
if (is_error($token)) {
    return '';
}
$barcode = array(
    "expire_seconds" => "259200",
    "action_name" => "QR_SCENE",
    "action_info" => array("scene" => array("scene_id" => $scene_id)),
);
$qrCode = $acc->barCodeCreateDisposable($barcode); // 生成临时二维码
// $qrCode = $acc->barCodeCreateFixed($barcode); // 生成永久二维码
if (is_error($qrCode)) {
    return '';
}

// 二维码链接地址
$qrCodeUrl = "https://mp.weixin.qq.com/cgi-bin/showqrcode?ticket=" . $qrCode['ticket'];

// 通过临时二维码进行的活动 需要回复公众号关注者信息时，需要添加qrcode表数据，api.php才能找到相应的模块 调用模块Processor的respond()方法
$qrcode = array(
    'uniacid' => $_W['uniacid'],
    'acid' => $_W['acid'],
    'qrcid' => $scene_id,
    'scene_str' => '',
    'keyword' => $qrkey['keyword'], // 关键字
    'name' => $qrkey['name'], // 活动标题一类的
    'model' => 1,
    'ticket' => $qrCode['ticket'],
    'url' => $qrCode['url'],
    'expire' => $qrCode['expire_seconds'],
    'createtime' => TIMESTAMP,
    'status' => '1',
    'type' => $this->modulename,
);
pdo_insert('qrcode', $qrcode);
```
#### respond()方法中 $this->message 的值
```php
// 关键字返回信息
array (
  'tousername' => '原始ID',
  'fromusername' => 'openid',
  'createtime' => '1484890997',
  'msgtype' => 'text',
  'content' => '红包',
  'msgid' => '',
  'from' => 'openid',
  'to' => '原始ID',
  'time' => '1484890997',
  'type' => 'text',
  'event' => NULL,
  'redirection' => false,
  'source' => NULL,
)
// 语音返回信息
array (
  'tousername' => '原始ID',
  'fromusername' => 'openid',
  'createtime' => '1484890152',
  'msgtype' => 'voice',
  'mediaid' => '',
  'format' => 'amr',
  'msgid' => '',
  'recognition' => '新年快乐。',
  'from' => 'openid',
  'to' => '原始ID',
  'time' => '1484890152',
  'type' => 'voice',
  'event' => NULL,
)
// 扫码返回信息
array (
  'tousername' => '原始ID',
  'fromusername' => 'openid',
  'createtime' => '1484890281',
  'msgtype' => 'event',
  'event' => 'SCAN',
  'eventkey' => '66',
  'ticket' => '',
  'from' => 'openid',
  'to' => '原始ID',
  'time' => '1484890281',
  'type' => 'text',
  'scene' => '66',
  'redirection' => true,
  'source' => 'qr',
  'content' => '红包',
)
```