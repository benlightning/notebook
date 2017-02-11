## 发红包代码记录
```php
/*
$config = $this->module['config'];
$account = account_fetch($acid);
$uniacid = pdo_fetchcolumn("SELECT uniacid FROM " . tablename('account') . " WHERE acid=:acid", array(':acid' => $acid));
$paysetting = uni_setting($uniacid, array('payment'));
$wechatpay = $paysetting['payment']['wechat'];
$wechat = array(
    'appid' => $account['key'],
    'mchid' => $wechatpay['mchid'],
    'apikey' => $wechatpay['apikey'],
    'pemname' => $config['pemname'],
    'ip' => $config['ip'],
);
 */
private function sendMoney($wechat, $openid, $money, $desc, $trade_no = '') {
    $desc = isset($desc) ? $desc : '活动红包';
    $money = $money * 100;
    $url = "https://api.mch.weixin.qq.com/mmpaymkttransfers/promotion/transfers";
    $pars = array();
    $pars['mch_appid'] = $wechat['appid'];
    $pars['mchid'] = $wechat['mchid'];
    $pars['nonce_str'] = random(32);
    $pars['partner_trade_no'] = empty($trade_no) ? $wechat['mchid'] . date('Ymd') . rand(1000000000, 9999999999) : $trade_no;
    $pars['openid'] = $openid;
    $pars['check_name'] = 'NO_CHECK';
    $pars['amount'] = $money;
    $pars['desc'] = $desc;
    $pars['spbill_create_ip'] = isset($wechat['ip']) ? $wechat['ip'] : $_SERVER['SERVER_ADDR'];
    ksort($pars, SORT_STRING);
    $string1 = '';
    foreach ($pars as $k => $v) {
        $string1 .= "{$k}={$v}&";
    }
    $string1 .= "key={$wechat['apikey']}";
    $pars['sign'] = strtoupper(md5($string1));
    $xml = array2xml($pars);
    $extras = array();
    $extras['CURLOPT_CAINFO'] = ATTACHMENT_ROOT . '/cert/rootca.pem.' . $wechat['pemname'];
    $extras['CURLOPT_SSLCERT'] = ATTACHMENT_ROOT . '/cert/apiclient_cert.pem.' . $wechat['pemname'];
    $extras['CURLOPT_SSLKEY'] = ATTACHMENT_ROOT . '/cert/apiclient_key.pem.' . $wechat['pemname'];

    load()->func('communication');
    $procResult = null;
    $response = ihttp_request($url, $xml, $extras);
    if ($response['code'] == 200) {
        $responseObj = simplexml_load_string($response['content'], 'SimpleXMLElement', LIBXML_NOCDATA);
        $responseObj = (array) $responseObj;
        $return['code'] = $responseObj['return_code'];
        $return['result_code'] = $responseObj['result_code'];
        $return['err_code'] = $responseObj['err_code'];
        $return['msg'] = $responseObj['return_msg'];
        $return['trade_no'] = $pars['partner_trade_no']; //返回订单号 用于重试
        return $return;
    }
}

private function sendHongbao($wechat, $openid, $money, $send_name, $act_name, $wishing, $trade_no = '') {
    $money = $money * 100;
    $num = 1;
    $url = "https://api.mch.weixin.qq.com/mmpaymkttransfers/sendredpack";
    $pars = array();
    $pars['wxappid'] = $wechat['appid'];
    $pars['mch_id'] = $wechat['mchid'];
    $pars['nonce_str'] = random(32);
    $pars['mch_billno'] = empty($trade_no) ? $wechat['mchid'] . date('Ymd') . rand(1000000000, 9999999999) : $trade_no;
    $pars['send_name'] = $send_name;
    $pars['re_openid'] = $openid;
    $pars['total_amount'] = $money;
    $pars['total_num'] = $num;
    $pars['wishing'] = $wishing;
    $pars['client_ip'] = isset($wechat['ip']) ? $wechat['ip'] : $_SERVER['SERVER_ADDR'];
    $pars['act_name'] = $act_name;
    $pars['remark'] = $act_name;
    ksort($pars, SORT_STRING);
    $string1 = '';
    foreach ($pars as $k => $v) {
        $string1 .= "{$k}={$v}&";
    }
    $string1 .= "key={$wechat['apikey']}";
    $pars['sign'] = strtoupper(md5($string1));
    $xml = array2xml($pars);
    $extras = array();
    $extras['CURLOPT_CAINFO'] = ATTACHMENT_ROOT . '/cert/rootca.pem.' . $wechat['pemname'];
    $extras['CURLOPT_SSLCERT'] = ATTACHMENT_ROOT . '/cert/apiclient_cert.pem.' . $wechat['pemname'];
    $extras['CURLOPT_SSLKEY'] = ATTACHMENT_ROOT . '/cert/apiclient_key.pem.' . $wechat['pemname'];
    load()->func('communication');
    $procResult = null;
    $response = ihttp_request($url, $xml, $extras);
    if ($response['code'] == 200) {
        $responseObj = simplexml_load_string($response['content'], 'SimpleXMLElement', LIBXML_NOCDATA);
        $responseObj = (array) $responseObj;
        $return['code'] = $responseObj['return_code'];
        $return['result_code'] = $responseObj['result_code'];
        $return['err_code'] = $responseObj['err_code'];
        $return['msg'] = $responseObj['return_msg'];
        $return['trade_no'] = $pars['mch_billno']; //返回订单号 用于重试
        return $return;
    }
}

private function sendHongbaoGroup($wechat, $openid, $money, $send_name, $act_name, $wishing, $num, $trade_no = '') {
    $money = $money * 100;
    $url = "https://api.mch.weixin.qq.com/mmpaymkttransfers/sendgroupredpack";
    $pars = array();
    $pars['wxappid'] = $wechat['appid'];
    $pars['mch_id'] = $wechat['mchid'];
    $pars['nonce_str'] = random(32);
    $pars['mch_billno'] = empty($trade_no) ? $wechat['mchid'] . date('Ymd') . rand(1000000000, 9999999999) : $trade_no;
    $pars['send_name'] = $send_name;
    $pars['re_openid'] = $openid;
    $pars['total_amount'] = $money;
    $pars['total_num'] = $num;
    $pars['amt_type'] = 'ALL_RAND';
    $pars['wishing'] = $wishing;
    //$pars['client_ip'] = isset($wechat['ip']) ? $wechat['ip'] : $_SERVER['SERVER_ADDR'];
    $pars['act_name'] = $act_name;
    $pars['remark'] = $act_name;
    ksort($pars, SORT_STRING);
    $string1 = '';
    foreach ($pars as $k => $v) {
        $string1 .= "{$k}={$v}&";
    }
    $string1 .= "key={$wechat['apikey']}";
    $pars['sign'] = strtoupper(md5($string1));
    $xml = array2xml($pars);
    $extras = array();
    $extras['CURLOPT_CAINFO'] = ATTACHMENT_ROOT . '/cert/rootca.pem.' . $wechat['pemname'];
    $extras['CURLOPT_SSLCERT'] = ATTACHMENT_ROOT . '/cert/apiclient_cert.pem.' . $wechat['pemname'];
    $extras['CURLOPT_SSLKEY'] = ATTACHMENT_ROOT . '/cert/apiclient_key.pem.' . $wechat['pemname'];
    load()->func('communication');
    $procResult = null;
    $response = ihttp_request($url, $xml, $extras);
    if ($response['code'] == 200) {
        $responseObj = simplexml_load_string($response['content'], 'SimpleXMLElement', LIBXML_NOCDATA);
        $responseObj = (array) $responseObj;
        $return['code'] = $responseObj['return_code'];
        $return['result_code'] = $responseObj['result_code'];
        $return['err_code'] = $responseObj['err_code'];
        $return['msg'] = $responseObj['return_msg'];
        $return['trade_no'] = $pars['mch_billno']; //返回订单号 用于重试
        return $return;
    }
}
```