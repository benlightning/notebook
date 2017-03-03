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

判断是否关注 1 关注，0未关注
```php
$follow = pdo_fetchcolumn("select follow from " . tablename('mc_mapping_fans') . " where openid=:openid and uniacid=:uniacid order by `fanid` desc", array(":openid" => $_W['openid'], ":uniacid" => $_W['uniacid']));
```

## 手册地址
* http://www.kancloud.cn/donknap/we7/136556
* (old)http://www.we7.cc/manual/dev:standards