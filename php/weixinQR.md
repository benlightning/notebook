```php
class WeixinQrcode {

    static $qrcode_url = "https://api.weixin.qq.com/cgi-bin/qrcode/create?";
    static $token_url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&";
    static $qrcode_get_url = "https://mp.weixin.qq.com/cgi-bin/showqrcode?";
    static $template_send_url = "https://api.weixin.qq.com/cgi-bin/message/template/send?";

    public function getQrcodeurl($ACCESS_TOKEN, $fqid, $type = 1, $timeout = 604800) {
        $url = self::$qrcode_url . 'access_token=' . $ACCESS_TOKEN;
        if ($type == 1) {
            //生成永久二维码
            $qrcode = '{"action_name": "QR_LIMIT_SCENE", "action_info": {"scene": {"scene_id": ' . $fqid . '}}}';
        } else {
            //生成临时二维码
            $qrcode = '{"expire_seconds": ' . $timeout . ', "action_name": "QR_SCENE", "action_info": {"scene": {"scene_id": ' . $fqid . '}}}';
        }
        $result = $this->http_post_data($url, $qrcode);
        $oo = json_decode($result[1]);
        if (!$oo->ticket) {
            $this->ErrorLogger('getQrcodeurl falied. Error Info: getQrcodeurl get failed');
            return '';
        }
        $url = self::$qrcode_get_url . 'ticket=' . $oo->ticket;
        return $url;
    }

    /**
     * 发送模板消息
     * @param array $data 消息结构
     * ｛
      "touser":"OPENID",
      "template_id":"ngqIpbwh8bUfcSsECmogfXcV14J0tQlEpBO27izEYtY",
      "url":"http://weixin.qq.com/download",
      "topcolor":"#FF0000",
      "data":{
      "参数名1": {
      "value":"参数",
      "color":"#173177"	 //参数颜色
      },
      "Date":{
      "value":"06月07日 19时24分",
      "color":"#173177"
      },
      "CardNumber":{
      "value":"0426",
      "color":"#173177"
      },
      "Type":{
      "value":"消费",
      "color":"#173177"
      }
      }
      }
     * @return boolean|array
     */
    public function sendTemplateMessage($token, $data) {
        $result = $this->http_post_data(self::$template_send_url. 'access_token=' . $token, json_encode($data));
        if ($result[1]) {
            $json = json_decode($result[1], true);
            if (!$json || !empty($json['errcode'])) {
                $this->ErrorLogger('errcode:'.$json['errcode'].' send template message falied. Error Info: '.$json['errmsg']);
                return false;
            }
            return $json;
        }
        return false;
    }

    public function getToken($appid, $secret,$force = false) {
        $access = cache_load('access_token:__weixin');
        if (empty($access) || $access['expire'] < time() || $force) {
            $ACCESS_TOKEN = file_get_contents(self::$token_url . "appid=$appid&secret=$secret");
            $ACCESS_TOKEN = json_decode($ACCESS_TOKEN);
            $ACCESS_TOKEN = $ACCESS_TOKEN->access_token;
            cache_write('access_token:__weixin', array('access_token' => $ACCESS_TOKEN, 'expire' => strtotime('+1 hour')));
            return $ACCESS_TOKEN;
        } else {
            return $access['access_token'];
        }
    }

    protected function http_post_data($url, $data_string) {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $data_string);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            'Content-Type: application/json;charset=utf-8',
            'Content-Length: ' . strlen($data_string))
        );
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);
        curl_setopt($ch, CURLOPT_SSLVERSION, 1);
        if (defined('CURL_SSLVERSION_TLSv1')) {
            curl_setopt($ch, CURLOPT_SSLVERSION, CURL_SSLVERSION_TLSv1);
        }
        ob_start();
        curl_exec($ch);
        if (curl_errno($ch)) {
            $this->ErrorLogger('curl falied. Error Info: ' . curl_error($ch));
        }
        $return_content = ob_get_contents();
        ob_end_clean();
        $return_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        return array($return_code, $return_content);
    }

    //下载二维码到服务器
    protected function DownLoadQr($url, $filestring) {
        if ($url == "") {
            return false;
        }
        $filename = $filestring . '.jpg';
        ob_start();
        readfile($url);
        $img = ob_get_contents();
        ob_end_clean();
        $size = strlen($img);
        $fp = fopen('./uploads/qrcode/' . $filename, "a");
        if (fwrite($fp, $img) === false) {
            $this->ErrorLogger('dolwload image falied. Error Info: 无法写入图片');
            exit();
        }
        fclose($fp);
        return './Uploads/qrcode/' . $filename;
    }

    private function ErrorLogger($errMsg) {
        $logger = fopen('./errorlog.txt', 'a+');
        fwrite($logger, date('Y-m-d H:i:s') . " Error Info : " . $errMsg . "\r\n");
    }

}

```