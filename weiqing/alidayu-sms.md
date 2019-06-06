```php
<?php
/**
 * [Weizan System] Copyright (c) 2013 012wz.com
 * $sn$
 */
defined('IN_IA') or exit('Access Denied');
load()->func('communication');

/*
 * $type 短信类型
 * $mobile 手机号
 * $content 参数值
 $content = array('code' => $code, 'product' => '验证模块');
 $ret = sms_send('verify_code_tpl', $_GPC['mobile'], $content);
 * */
function sms_send($type, $mobile, $content) {
    global $_W;
    if(!is_array($_W['module_name']['config']['sms'])) {// || !$_W['module_name']['config']['sms']['status']
        return error(-1, '没有设置短信参数或已关闭短信功能');
    }
    $post = array(
        'method' => 'alibaba.aliqin.fc.sms.num.send',
        'app_key' => $_W['module_name']['config']['sms']['key'],
        'timestamp' => date('Y-m-d H:i:s'),
        'format' => 'json',
        'v' => '2.0',
        'sign_method' => 'md5',
        'sms_type' => 'normal',
        'sms_free_sign_name' => $_W['module_name']['config']['sms']['sign'],
        'rec_num' => $mobile,
        'sms_template_code' => $_W['module_name']['config']['sms'][$type],
        'sms_param' => json_encode($content)
    );

    ksort($post);
    $str = '';
    foreach($post as $key => $val) {
        $str .= $key.$val;
    }
    $secret = $_W['module_name']['config']['sms']['secret'];
    $post['sign'] = strtoupper(md5($secret . $str . $secret));
    $query = '';
    foreach($post as $key => $val) {
        $query .= "{$key}=" . urlencode($val) . "&";
    }
    $query = substr($query, 0, -1);
    $url = 'http://gw.api.taobao.com/router/rest?' . $query;
    $result = ihttp_get($url);
    if(is_error($result)) {
        return $result;
    }
    $result = @json_decode($result['content'], true);
    if(!empty($result['error_response'])) {
        if(isset($result['error_response']['sub_code'])) {
            $msg = sms_error_code($result['error_response']['sub_code']);
        } else {
            $msg['msg'] = $result['error_response']['msg'];
        }
        return error(-1, $msg['msg']);
    }
    sms_insert_send_log($type, $mobile, $content['code']);
    return true;
}

/**
 * 电话通知
 */
function sms_singlecall($called_num, $content, $type = 'clerk') {
    global $_W;
    $config = $_W['module_name']['config']['sms'];
    if(!is_array($_W['module_name']['config']['sms'])) { // || !$_W['module_name']['config']['sms']['status']
        return error(-1, '没有设置短信参数或已关闭短信功能');
    }
    if(!is_array($config[$type]) || !$config[$type]['status']) {
        return error(-1, '没有开启电话通知功能');
    }
    $post = array(
        'method' => 'alibaba.aliqin.fc.tts.num.singlecall',
        'app_key' => $config['key'],
        'timestamp' => date('Y-m-d H:i:s'),
        'format' => 'json',
        'v' => '2.0',
        'sign_method' => 'md5',
        'called_num' => $called_num,
        'called_show_num' => $config[$type]['called_show_num'],
        'tts_code' => $config[$type]['tts_code'],
        'tts_param' => json_encode($content)
    );

    ksort($post);
    $str = '';
    foreach($post as $key => $val) {
        $str .= $key.$val;
    }
    $secret = $config['secret'];
    $post['sign'] = strtoupper(md5($secret . $str . $secret));
    $query = '';
    foreach($post as $key => $val) {
        $query .= "{$key}=" . urlencode($val) . "&";
    }
    $query = substr($query, 0, -1);
    $url = 'http://gw.api.taobao.com/router/rest?' . $query;
    $result = ihttp_get($url);
    if(is_error($result)) {
        return $result;
    }
    $result = @json_decode($result['content'], true);
    if(!empty($result['error_response'])) {
        $msg = $result['error_response']['sub_msg'] ? $result['error_response']['sub_msg'] : $result['error_response']['msg'];
        return error(-1, $msg);
    }
    return true;
}

function sms_insert_send_log($type, $mobile,$captcha) {
    global $_W;
    $data = array(
        'uniacid' => $_W['uniacid'],
            'openid' => $_W['openid'],
        'type' => $type,
        'mobile' => $mobile,
        'sendtime' => TIMESTAMP,
            'captcha'=>$captcha,
    );
    pdo_insert('sms_sendlog', $data);
    return true;
}

function sms_types() {
    return array(
        'verify_code_tpl' => '手机验证验证码'
    );
}

function sms_error_code($code) {
    $msgs = array(
        'isv.OUT_OF_SERVICE' => array(
            'msg' => '业务停机',
            'handle' => '登陆www.alidayu.com充值',
        ),
        'isv.PRODUCT_UNSUBSCRIBE' => array(
            'msg' => '产品服务未开通',
            'handle' => '登陆www.alidayu.com开通相应的产品服务',
        ),
        'isv.ACCOUNT_NOT_EXISTS' => array(
            'msg' => '账户信息不存在',
            'handle' => '登陆www.alidayu.com完成入驻',
        ),

        'isv.ACCOUNT_ABNORMAL' => array(
            'msg' => '账户信息异常',
            'handle' => '联系技术支持',
        ),

        'isv.SMS_TEMPLATE_ILLEGAL' => array(
            'msg' => '模板不合法',
            'handle' => '登陆www.alidayu.com查询审核通过短信模板使用',
        ),

        'isv.SMS_SIGNATURE_ILLEGAL' => array(
            'msg' => '签名不合法',
            'handle' => '登陆www.alidayu.com查询审核通过的签名使用',
        ),
        'isv.MOBILE_NUMBER_ILLEGAL' => array(
            'msg' => '手机号码格式错误',
            'handle' => '使用合法的手机号码',
        ),
        'isv.MOBILE_COUNT_OVER_LIMIT' => array(
            'msg' => '手机号码数量超过限制',
            'handle' => '批量发送，手机号码以英文逗号分隔，不超过200个号码',
        ),

        'isv.TEMPLATE_MISSING_PARAMETERS' => array(
            'msg' => '短信模板变量缺少参数',
            'handle' => '确认短信模板中变量个数，变量名，检查传参是否遗漏',
        ),
        'isv.INVALID_PARAMETERS' => array(
            'msg' => '参数异常',
            'handle' => '检查参数是否合法',
        ),
        'isv.BUSINESS_LIMIT_CONTROL' => array(
            'msg' => '触发业务流控限制',
            'handle' => '短信验证码，使用同一个签名，对同一个手机号码发送短信验证码，允许每分钟1条，累计每小时7条。 短信通知，使用同一签名、同一模板，对同一手机号发送短信通知，允许每天50条（自然日）',
        ),

        'isv.INVALID_JSON_PARAM' => array(
            'msg' => '触发业务流控限制',
            'handle' => 'JSON参数不合法    JSON参数接受字符串值',
        ),
    );
    return $msgs[$code];
}

require_once 'SignatureHelper.php';
/**
 * 2017-05-25 后注册的 阿里短信
 * @param string $phone
 * @param string $SignName
 * @param $tpl
 * @param $content
 * @return mixed
 */
function new_send_sms($phone='',$SignName='',$tpl,$content) {

    $params = array ();

    // *** 需用户填写部分 ***

    // fixme 必填: 请参阅 https://ak-console.aliyun.com/ 取得您的AK信息
    $accessKeyId = "";
    $accessKeySecret = "";

    // fixme 必填: 短信接收号码
    $params["PhoneNumbers"] = $phone;

    // fixme 必填: 短信签名，应严格按"签名名称"填写，请参考: https://dysms.console.aliyun.com/dysms.htm#/develop/sign
    $params["SignName"] = $SignName;

    // fixme 必填: 短信模板Code，应严格按"模板CODE"填写, 请参考: https://dysms.console.aliyun.com/dysms.htm#/develop/template
    $params["TemplateCode"] = $tpl;//"SMS_0000001";

    // fixme 可选: 设置模板参数, 假如模板中存在变量需要替换则为必填项
    $params['TemplateParam'] = $content;
//            Array (
//            "code" => "12345",
//            "product" => "阿里通信"
//        );

    // fixme 可选: 设置发送短信流水号
//        $params['OutId'] = "12345";

    // fixme 可选: 上行短信扩展码, 扩展码字段控制在7位或以下，无特殊需求用户请忽略此字段
//        $params['SmsUpExtendCode'] = "1234567";


    // *** 需用户填写部分结束, 以下代码若无必要无需更改 ***
    if(!empty($params["TemplateParam"]) && is_array($params["TemplateParam"])) {
        $params["TemplateParam"] = json_encode($params["TemplateParam"], JSON_UNESCAPED_UNICODE);
    }

    // 初始化SignatureHelper实例用于设置参数，签名以及发送请求
    $helper = new SignatureHelper();

    // 此处可能会抛出异常，注意catch
    $content = $helper->request(
        $accessKeyId,
        $accessKeySecret,
        "dysmsapi.aliyuncs.com",
        array_merge($params, array(
            "RegionId" => "cn-hangzhou",
            "Action" => "SendSms",
            "Version" => "2017-05-25",
        ))
    // fixme 选填: 启用https
    // ,true
    );

    return $content;
}

/**
 *SignatureHelper.php文件，可以到阿里短信官网下载到
 */
<?php

/**
 * 签名助手 2017/11/19
 *
 * Class SignatureHelper
 */
class SignatureHelper {

    /**
     * 生成签名并发起请求
     *
     * @param $accessKeyId string AccessKeyId (https://ak-console.aliyun.com/)
     * @param $accessKeySecret string AccessKeySecret
     * @param $domain string API接口所在域名
     * @param $params array API具体参数
     * @param $security boolean 使用https
     * @return bool|\stdClass 返回API接口调用结果，当发生错误时返回false
     */
    public function request($accessKeyId, $accessKeySecret, $domain, $params, $security=false) {
        $apiParams = array_merge(array (
            "SignatureMethod" => "HMAC-SHA1",
            "SignatureNonce" => uniqid(mt_rand(0,0xffff), true),
            "SignatureVersion" => "1.0",
            "AccessKeyId" => $accessKeyId,
            "Timestamp" => gmdate("Y-m-d\TH:i:s\Z"),
            "Format" => "JSON",
        ), $params);
        ksort($apiParams);

        $sortedQueryStringTmp = "";
        foreach ($apiParams as $key => $value) {
            $sortedQueryStringTmp .= "&" . $this->encode($key) . "=" . $this->encode($value);
        }

        $stringToSign = "GET&%2F&" . $this->encode(substr($sortedQueryStringTmp, 1));

        $sign = base64_encode(hash_hmac("sha1", $stringToSign, $accessKeySecret . "&",true));

        $signature = $this->encode($sign);

        $url = ($security ? 'https' : 'http')."://{$domain}/?Signature={$signature}{$sortedQueryStringTmp}";

        try {
            $content = $this->fetchContent($url);
            return json_decode($content);
        } catch( \Exception $e) {
            return false;
        }
    }

    private function encode($str)
    {
        $res = urlencode($str);
        $res = preg_replace("/\+/", "%20", $res);
        $res = preg_replace("/\*/", "%2A", $res);
        $res = preg_replace("/%7E/", "~", $res);
        return $res;
    }

    private function fetchContent($url) {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_TIMEOUT, 5);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            "x-sdk-client" => "php/2.0.0"
        ));

        if(substr($url, 0,5) == 'https') {
            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
            curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
        }

        $rtn = curl_exec($ch);

        if($rtn === false) {
            trigger_error("[CURL_" . curl_errno($ch) . "]: " . curl_error($ch), E_USER_ERROR);
        }
        curl_close($ch);

        return $rtn;
    }
}
```