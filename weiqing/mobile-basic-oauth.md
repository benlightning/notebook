# 需要网页授权的添加此方法调用

```php
public function basicOauth() {
    global $_GPC, $_W;
    $user_agent = $_SERVER['HTTP_USER_AGENT'];
    if (strpos($user_agent, 'MicroMessenger') === false) {
        message('本页面仅支持微信访问!非微信浏览器禁止浏览!', '', 'error');
    }
    if (empty($_W['openid'])) {
        message('获取不到您的OpenID,请从新进入页面!', '', 'error');
    }
    if (empty($_W['fans']['nickname'])) {
        load()->model('mc');
        $info = mc_oauth_userinfo();
        $_W['fans']['nickname'] = $info['nickname'];
        $_W['fans']['tag']['avatar'] = $info['avatar'];
    }
}
// 判断是否关注 $follow['follow'] == 1
$follow = mc_fansinfo($openid, $_W['acid'], $_W['uniacid']);
```