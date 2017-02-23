# 上传媒体文件的一些操作

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

```javascript
wx.chooseImage({
    count: 1, // 默认9
    sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
    sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
    success: function (res) {
        var localIds = res.localIds; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
        that.find('img').attr('src', localIds[0]);
        wx.uploadImage({
            localId: localIds[0], // 需要上传的图片的本地ID，由chooseImage接口获得
            isShowProgressTips: 1, // 默认为1，显示进度提示
            success: function (res) {
                var serverId = res.serverId; // 返回图片的服务器端ID
                that.find('input').val(serverId);
            }
        });
    }
});
```