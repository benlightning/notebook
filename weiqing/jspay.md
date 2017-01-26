## js代码部分
```
function invokepay(type) {
    $.getJSON("getJSpayParamUrl", {}, function (data, status) {
        if (status == 'success' && data.error == 0) {
//                                wx.chooseWXPay({
//                                    timestamp: data.wechat.timeStamp, // 支付签名时间戳，注意微信jssdk中的所有使用timestamp字段均为小写。但最新版的支付后台生成签名使用的timeStamp字段名需大写其中的S字符
//                                    nonceStr: data.wechat.nonceStr, // 支付签名随机串，不长于 32 位
//                                    package: data.wechat.package, // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=***）
//                                    signType: data.wechat.signType, // 签名方式，默认为'SHA1'，使用新版支付需传入'MD5'
//                                    paySign: data.wechat.paySign, // 支付签名
//                                    success: function (res) {
//                                        alert('支付成功');
//                                        // 支付成功后的回调函数
//                                    },
//                                    cancel: function (res) {
//                                        console.log(res);
//                                        //支付取消
//                                    },
//                                    error: function (res) {
//                                        console.log(res);
//                                        alert('支付系统错误'+res.err_msg);
//                                    }
//                                });
            WeixinJSBridge.invoke('getBrandWCPayRequest', {
                'appId': data.wechat.appid ? data.wechat.appid : data.wechat.appId,
                'timeStamp': data.wechat.timeStamp,
                'nonceStr': data.wechat.nonceStr,
                'package': data.wechat.package,
                'signType': data.wechat.signType,
                'paySign': data.wechat.paySign,
            }, function (res) {
                if (res.err_msg == 'get_brand_wcpay_request:ok') {
                    //支付成功回调
                    alert('支付成功');
                } else if (res.err_msg == 'get_brand_wcpay_request:cancel') {
                    console.log(res);
                    //alert("取消支付")
                } else {
                    console.log(res);
                    alert('支付出错，请联系客服');
                    //alert(res.err_msg)
                }
            });
        } else {
            alert('支付出错，请重试');
        }
    });
}
```
## php代码部分
```
function doMobilePay(){
    global $_W,$_GPC;
    if ($_W['isajax']) {
        $params = array(
            'module' => $this->modulename,
            'type' => 'wechat',
            'uniacid' => $_W['uniacid'],
            'acid' => $_W['acid'],
            'openid' => $_W['fans']['from_user'],
            'payopenid' => $_W['fans']['from_user'],
            'title' => '描述标题',
            'fee' => 1, //金额
        );
        $params['attach'] = $_W['uniacid'];
        $setting = uni_setting($_W['uniacid'], array('payment'));
        if (!is_array($setting['payment'])) {
            exit(json_encode(array('error' => 1, 'msg' => '没有设定支付参数.')));
        }

        $wechat = $setting['payment']['wechat'];
        $sql = 'SELECT `key`,`secret` FROM ' . tablename('account_wechats') . ' WHERE `acid`=:acid';
        $row = pdo_fetch($sql, array(':acid' => $wechat['account']));
        $wechat['appid'] = $row['key'];
        $wechat['secret'] = $row['secret'];
        $params['tid'] = $tid; // $tid 你要传入的tid,用于回调处理你的业务逻辑
        $wOpt = $this->wechat_build($params, $wechat);
        if (is_error($wOpt)) {
            if ($wOpt['message'] == 'invalid out_trade_no' || $wOpt['message'] == 'OUT_TRADE_NO_USED') {
                exit(json_encode(array('error' => 1, 'msg' => '抱歉，发起支付失败，系统已经修复此问题，请重新尝试支付。')));
            }
            exit(json_encode(array('error' => 1, 'msg' => '抱歉，发起支付失败，具体原因为：“' . $wOpt['errno'] . ':' . $wOpt['message'] . '”。请及时联系站点管理员。')));
        }

        echo json_encode(array('error' => 0, 'wechat' => $wOpt));
    }
}
public function payResult($params) {
    global $_W;
    //一些业务代码
    //根据参数params中的result来判断支付是否成功
    if ($params['result'] == 'success' && $params['from'] == 'notify') {
        $tid = intval($params['tid']);//前面传入的tid参数
        
        $uniontid = $params['uniontid'] ? $params['uniontid'] : '';
        //此处会处理一些支付成功的业务代码
    }
    //因为支付完成通知有两种方式 notify，return,notify为后台通知,return为前台通知，需要给用户展示提示信息
    //return做为通知是不稳定的，用户很可能直接关闭页面，所以状态变更以notify为准
    //如果消息是用户直接返回（非通知），则提示一个付款成功
    //如果是JS版的支付此处的跳转则没有意义
    if ($params['from'] == 'return') {
        if ($params['result'] == 'success') {
            message('支付成功！', $this->createMobileUrl('url'), 'success');
        } else {
            message('支付失败！', $this->createMobileUrl('url'), 'error');
        }
    }
}
public function wechat_build($params, $wechat) {
    global $_W;
    load()->func('communication');
    $paylog = pdo_fetch("SELECT * FROM " . tablename('core_paylog') . " WHERE `uniacid`=:uniacid AND `module`=:module AND `tid`=:tid ", array(':uniacid' => $params['uniacid'], ':module' => $params['module'], ':tid' => $params['tid']));
    if (!empty($paylog) && $paylog['status'] != '0') {
        return error(-1, '已经支付成功, 不需要重复支付.');
    }
    $moduleid = pdo_fetchcolumn("SELECT mid FROM " . tablename('modules') . " WHERE name = :name", array(':name' => $params['module']));
    $moduleid = empty($moduleid) ? '000000' : sprintf("%06d", $moduleid);
    $fee = $params['fee'];
    if (!empty($paylog) && $paylog['status'] == '0') { //订单存在并且未支付，更新记录
        $fee = $params['fee'];
        $record = array();
        $record['fee'] = $fee;
        $record['uniontid'] = date('YmdHis') . $moduleid . random(8, 1);
        $record['card_fee'] = $fee;
        pdo_update('core_paylog', $record, array('tid' => $params['tid'], 'module' => $params['module']));
    }
    if (empty($paylog)) {//订单不存在，加入记录
        $fee = $params['fee'];
        $record = array();
        $record['uniacid'] = $params['uniacid'];
        $record['openid'] = $params['openid'];
        $record['module'] = $params['module'];
        $record['type'] = $params['type'];
        $record['tid'] = $params['tid'];
        $record['uniontid'] = date('YmdHis') . $moduleid . random(8, 1);
        $record['fee'] = $fee;
        $record['status'] = '0';
        $record['is_usecard'] = 0;
        $record['card_id'] = 0;
        $record['card_fee'] = $fee;
        $record['encrypt_code'] = '';
        $record['acid'] = $params['acid'];
        pdo_insert('core_paylog', $record);
    }
    $params['uniontid'] = $record['uniontid'];
    load()->func('communication');
    if (empty($wechat['version']) && !empty($wechat['signkey'])) {
        $wechat['version'] = 1;
    }
    $wOpt = array();
    if ($wechat['version'] == 1) {
        $wOpt['appId'] = $wechat['appid'];
        $wOpt['timeStamp'] = TIMESTAMP . "";
        $wOpt['nonceStr'] = random(8);
        $package = array();
        $package['bank_type'] = 'WX';
        $package['body'] = $params['title'];
        $package['attach'] = $_W['uniacid'];
        $package['partner'] = $wechat['partner'];
        $package['out_trade_no'] = $params['uniontid'];
        $package['total_fee'] = $params['fee'] * 100;
        $package['fee_type'] = '1';
        $package['notify_url'] = $_W['siteroot'] . 'payment/wechat/notify.php';
        $package['spbill_create_ip'] = CLIENT_IP;
        $package['time_start'] = date('YmdHis', TIMESTAMP);
        $package['time_expire'] = date('YmdHis', TIMESTAMP + 600);
        $package['input_charset'] = 'UTF-8';
        ksort($package);
        $string1 = '';
        foreach ($package as $key => $v) {
            if (empty($v)) {
                continue;
            }
            $string1 .= "{$key}={$v}&";
        }
        $string1 .= "key={$wechat['key']}";
        $sign = strtoupper(md5($string1));

        $string2 = '';
        foreach ($package as $key => $v) {
            $v = urlencode($v);
            $string2 .= "{$key}={$v}&";
        }
        $string2 .= "sign={$sign}";
        $wOpt['package'] = $string2;

        $string = '';
        $keys = array('appId', 'timeStamp', 'nonceStr', 'package', 'appKey');
        sort($keys);
        foreach ($keys as $key) {
            $v = $wOpt[$key];
            if ($key == 'appKey') {
                $v = $wechat['signkey'];
            }
            $key = strtolower($key);
            $string .= "{$key}={$v}&";
        }
        $string = rtrim($string, '&');

        $wOpt['signType'] = 'SHA1';
        $wOpt['paySign'] = sha1($string);
        return $wOpt;
    } else {
        $package = array();
        $package['appid'] = $wechat['appid'];
        $package['mch_id'] = $wechat['mchid'];
        $package['nonce_str'] = random(8);
        $package['body'] = $params['title'];
        $package['attach'] = $_W['uniacid'];
        $package['out_trade_no'] = $params['uniontid'];
        $package['total_fee'] = $params['fee'] * 100;
        $package['spbill_create_ip'] = CLIENT_IP;
        $package['time_start'] = date('YmdHis', TIMESTAMP);
        $package['time_expire'] = date('YmdHis', TIMESTAMP + 600);
        $package['notify_url'] = $_W['siteroot'] . 'payment/wechat/notify.php';
        $package['trade_type'] = 'JSAPI';
        $package['openid'] = $_W['fans']['from_user'];
        ksort($package, SORT_STRING);
        $string1 = '';
        foreach ($package as $key => $v) {
            if (empty($v)) {
                continue;
            }
            $string1 .= "{$key}={$v}&";
        }
        $string1 .= "key={$wechat['signkey']}";
        $package['sign'] = strtoupper(md5($string1));
        $dat = array2xml($package);
        $response = ihttp_request('https://api.mch.weixin.qq.com/pay/unifiedorder', $dat);
        if (is_error($response)) {
            return $response;
        }
        $xml = @isimplexml_load_string($response['content'], 'SimpleXMLElement', LIBXML_NOCDATA);
        if (strval($xml->return_code) == 'FAIL') {
            return error(-1, strval($xml->return_msg));
        }
        if (strval($xml->result_code) == 'FAIL') {
            return error(-1, strval($xml->err_code) . ': ' . strval($xml->err_code_des));
        }
        $prepayid = $xml->prepay_id;
        $wOpt['appId'] = $wechat['appid'];
        $wOpt['timeStamp'] = TIMESTAMP . "";
        $wOpt['nonceStr'] = random(8);
        $wOpt['package'] = 'prepay_id=' . $prepayid;
        $wOpt['signType'] = 'MD5';
        ksort($wOpt, SORT_STRING);
        foreach ($wOpt as $key => $v) {
            $string .= "{$key}={$v}&";
        }
        $string .= "key={$wechat['signkey']}";
        $wOpt['paySign'] = strtoupper(md5($string));
        return $wOpt;
    }
    return $wOpt;
}
```