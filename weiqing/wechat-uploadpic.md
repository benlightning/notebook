# 微信js上传图片到微信服务器，并下载到本地

```javascript
wx.chooseImage({
    count: 1, // 默认9
    sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
    sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
    success: function (res) {
        var localIds = res.localIds; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
        //that.find('img').attr('src', localIds[0]);
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

## 微擎方法

```php
$account_api = WeAccount::create();
$fileurl = $account_api->downloadMedia(array('media_id'=>$_GPC['frontphoto']));
```