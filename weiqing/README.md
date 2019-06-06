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
* [阿里短信相关](alidayu-sms.md)
* [微信js上传图片到微信服务器，并下载到本地](wechat-uploadpic.md)
* [网页授权相关调用](mobile-basic-oauth.md)
* [processor相关](processor.md)
* [模块开发相关](addons-faq.md)
* [小程序开发相关](wxapp.md)

判断是否关注 1 关注，0未关注

```php
$follow = pdo_fetchcolumn("select follow from " . tablename('mc_mapping_fans') . " where openid=:openid and uniacid=:uniacid order by `fanid` desc", array(":openid" => $_W['openid'], ":uniacid" => $_W['uniacid']));
```

> 关于脏数据的问题，尤其是牵扯支付订单这些的，自建模块时会经常清空表，而订单记录一般需要保存到系统的core_playlog表中，此时truncate表时务必清空相应的core_playlog表数据

> 公众号设置主管理员后，可设置（可用应用模块/模板：会员权限组，应用权限组，应用权限组）可访问的权限（无主管理员时，创始人为默认主管理员，公众号拥有所有权限），不能对主管理员单独设置权限

> 公众号添加操作员，可对操作员设置权限，对应的模块权限会进入ims_users_permission表记录

## 手册地址

* http://www.kancloud.cn/donknap/we7/136556
* (old)http://www.we7.cc/manual/dev:standards
