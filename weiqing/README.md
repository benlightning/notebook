# 微擎使用记录

* [生产带参数二维码，response()参数信息](genQR.md)
* [接收到的语音解析](voicemsg.md)
* [发红包方法](sendRedpac.md)
* [manifest.xml](config-xml.md)
* [微信js发起支付](jspay.md)
* [下载csv](download.md)
* [微信上传媒体文件](uploadmedia.md)
* [模块定时任务linux下](linux-cron.md)
* [module.php文件设置](module-setting-rule.md)
* [阿里大鱼短信客服](alidayu-sms.md)
* [微信js上传图片到微信服务器，并下载到本地](wechat-uploadpic.md)
* [网页授权相关调用](mobile-basic-oauth.md)

判断是否关注 1 关注，0未关注
```php
$follow = pdo_fetchcolumn("select follow from " . tablename('mc_mapping_fans') . " where openid=:openid and uniacid=:uniacid order by `fanid` desc", array(":openid" => $_W['openid'], ":uniacid" => $_W['uniacid']));
```

> 关于脏数据的问题，尤其是牵扯支付订单这些的，自建模块时会经常清空表，而订单记录一般需要保存到系统的core_playlog表中，此时truncate表时务必清空相应的core_playlog表数据

## 手册地址
* http://www.kancloud.cn/donknap/we7/136556
* (old)http://www.we7.cc/manual/dev:standards

