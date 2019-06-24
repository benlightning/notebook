# 微擎常用函数

```text
## 常用不需要加载自带函数

写入cookie 过期时间秒,0表示跟随浏览器,false,不允许js读取
isetcookie($key, $value, $expire = 0, $httponly = false) 
获取客户端IP
$ip =  getip();
随机字符串,6是长度,1为是否数字,
random(6,true);
获取表明
tablename('account')
生成如下地址 :   ./index.php?i=5&c=site&a=entry&do=rank&m=yoby_test&op=del,true不带微信拼接字符,第二个true表示添加域名
$url  = murl('site/entry/rank', ['m'=>'yoby_test','op'=>'del'], 1,1);
$url  =  wurl('site/entry/rank',['m'=>'yoby_test']);
一般微信端
murl('entry', ['m'=>'yoby_diyform','do'=>'index','rid'=>$item['rid']], 1,1)
分页
pagination($total, $pageIndex, $pageSize = 15);
返回媒体或图片路径
tomedia($src)
查找字符串
strexists('helloworld', 'h');
截取字符串
cutstr($string,$length, TRUE,'utf-8');
计算字符串长度,中英文都为1
istrlen('hello-微擎');
字符串加密或解密,默认解密,ENCODE加密,key秘钥,0过期时间
authcode($string, $operation = 'DECODE', $key = '', $expiry = 0)
emoji_unicode_decode()emoji_unicode_encode 解码emoji
get_first_pinyin($str)拼音首字母
ihtml_entity_decode 显示html
array2xml xml2array xml数组互换
web默认加载,微信端也有以下函数

第二个参数为空,不跳转,warning警告,error错误,info提示,ajax
message('成功', referer(), 'success');
ajax 返回 {"message":"\u6210\u529f","redirect":"","type":"ajax"}
模板加载
template('common/template');
iajax(code,message,redirect);参数2支持数组或字符串
itoast("配置保存成功", referer(), 'success');

## 文件操作函数

load()->func('file');
file_copy(IA_ROOT . '/web', IA_ROOT . '/data', array('php')) 文件复制 过滤php 
file_delete('test.png');
file_image_crop(IA_ROOT . '/test.png', IA_ROOT . '/test2.png', 50, 50); 剪切
file_image_thumb(IA_ROOT . '/test.png', IA_ROOT . '/test2.png', 500); 生成缩率图 最后是宽
file_move(IA_ROOT . '/test.log', IA_ROOT . '/web/test.log');文件移动
file_upload($_FILE['test'], 'image', 'test.png'); 上传
file_write(IA_ROOT . '/test.log', 'hello-world');文件写入
mkdirs(IA_ROOT . '/web/hello/world/example');创建目录
file_remote_delete远程删除

写入远程
$filename ="images/$weid/yoby_tougao_".$snid.'.jpg';
file_write($filename, $data);
if (!empty($_W['setting']['remote']['type'])) { // 判断系统是否开启了远程附件
    $remotestatus = file_remote_upload($filename); //上传图片到远程
    if (is_error($remotestatus)) {
        message('远程附件上传失败，请检查配置并重新上传') ;
    }
}

## http操作函数

load()->func('communication');
$result = ihttp_get('http://x.com')['content'];
文件上传@+绝对路径
$result = ihttp_post('https://www.baidu.com', array('username' => 'we7'));
高级请求,第三个参数附加传入
ihttp_request($url, $post = '', $extra = array(), $timeout = 60)

## 记录日志

load()->func('logging');
logging_run('记录字符串日志数据');
logging_run([1,2,3]);

## 用户中心

load()->model('mc');
检测是否登录,没登陆跳到登陆
checkauth();用于微信端 checklogin()用于web
openid转换uid
$uid = $_W['member']['uid'];
Array
(
    [uid] => 10128
    [realname] => 用户昵称
    [mobile] => 手机号码
    [email] => 邮箱
    [groupid] => 用户组ID
    [groupname] => 用户组名称
    [credit1] => 积分
    [credit2] => 余额
    [credit3] => 其它积分
    [credit4] => 其它积分
    [credit5] => 其它积分
    [credit6] => 其它积分
)
$uid =  mc_openid2uid($openid);
$openid = mc_uid2openid($uid);
mc_credit_update($uid,'credit2', -10,[$uid,'测试减少10积分']);//增减积分
$num =  mc_credit_fetch($uid,['credit2']);//查询积分
积分通知
mc_notice_credit1($openid, $uid, 10, '充值积分到账10分', $url = 'http://w.cn', $remark = '谢谢惠顾，点击查看详情');
弹出填写信息
mc_require($openid,['realname', 'mobile']);
更新填写信息
mc_update($openid,['email' => 'test@163.com']);
查询用户信息uid,返回字段
mc_fansinfo($uidoropenid)
Oauth获取用户信息
mc_oauth_userinfo($acid)
通过openid获取
mc_oauth_fans($openid)
查询用户信息
mc_fetch($uid/openid,[])返回字段
uid,mobile,credit1积分,credit2余额,realname,nickname,avatar,gender 1男2女,residecity市

## 自定义函数

微信中做了base64的加密字符串解密,解密之前不需要base64解密
openssl_decrypt($wechat_data['req_info'], "AES-256-ECB", md5('appid'));
serialize unserialize 序列化
extract($arr);compact('a','b','c','d','i');解码成字符串
explode('-','1-2-3-4');//分割成数组
implode('-',$arr);//转换成字符串
mb_convert_encoding('李太白',"utf-8",'auto');自动转换成uft8

foreach($list as &$row){
处理数据
}
unset($row);

## 支付几种返回

$wechat = uni_setting(1, array('payment'));
借用返回
Array
(
    [payment] => Array
        (
            [credit] => Array
                (
                    [switch] => 
                )

            [alipay] => Array
                (
                    [switch] => 
                    [account] => 
                    [partner] => 
                    [secret] => 
                )

            [wechat] => Array
                (
                    [switch] => 2
                    [account] => 1
                    [signkey] => 
                    [partner] => 
                    [key] => 
                    [borrow] => 2
                )

            [delivery] => Array
                (
                    [switch] => 
                )

        )

)
本身返回
Array
(
    [payment] => Array
        (
            [wechat] => Array
                (
                    [switch] => 1
                    [version] => 2
                    [apikey] => J8BNZ88rR8hhHH9Z7bqhbB57HbQOp3F9
                    [mchid] => 1493418902
                    [account] => 2
                    [signkey] => J8BNZ88rR8hhHH9Z7bqhbB57HbQOp3F9
                )

        )

)
非支付id返回
Array
(
    [payment] => Array
        (
            [wechat] => Array
                (
                    [switch] => 2
                    [borrow] => 2
                    [account] => 3
                    [signkey] => 
                )

        )

)
```