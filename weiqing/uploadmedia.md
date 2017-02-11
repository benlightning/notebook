```php
// $fullname = MODULE_ROOT . '/images/xx.jpg';
public function uploadMedia($fullname) {
    global $_W;
    load()->model('account');
    $acc = WeAccount::create($_W['acid']);
    $token = $acc->getAccessToken();
    if (is_error($token)) {
        return '';
    }

    $sendapi = "https://api.weixin.qq.com/cgi-bin/material/add_material?access_token={$token}&type=image";
    $data = array(
        'media' => '@' . $fullname
    );
    load()->func('communication');
    $resp = @ihttp_request($sendapi, $data);
    if (is_error($resp)) {
        return '';
    }
    $content = @json_decode($resp['content'], true);
    if (empty($content)) {
        return '';
    }
    if (!empty($content['errcode'])) {
        return '';
    }
    return $content['media_id'];
}
```