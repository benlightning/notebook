```
public function respond() {
    global $_W;
    $type = $this->message['type'];
    $content = $this->message['content'];
    $fromuser = $this->message['from'];
    //这里定义此模块进行消息处理时的具体过程, 请查看微擎文档来编写你的代码
    //print_r($this->message);
    //读取rid 获取红包id  根据数组获取中奖概率
    if ($this->message['source'] == 'qr') {
        $scene = intval($this->message['scene']);
        // 参数二维码处理
    }

    if ($this->message['msgtype'] == 'voice') {
        $mediaid = $this->message['mediaid']; // 语音在服务器的标识
        $voice = $this->filter_mark($this->message['recognition']);
    }
}
private function filter_mark($text) { // 处理返回的语音内容
    if (trim($text) == '')
        return '';
    $text = preg_replace("/[[:punct:]\s]/", ' ', $text);
    $text = urlencode($text);
    $text = preg_replace("/(%7E|%60|%21|%40|%23|%24|%25|%5E|%26|%27|%2A|%28|%29|%2B|%7C|%5C|%3D|\-|_|%5B|%5D|%7D|%7B|%3B|%22|%3A|%3F|%3E|%3C|%2C|\.|%2F|%A3%BF|%A1%B7|%A1%B6|%A1%A2|%A1%A3|%A3%AC|%7D|%A1%B0|%A3%BA|%A3%BB|%A1%AE|%A1%AF|%A1%B1|%A3%FC|%A3%BD|%A1%AA|%A3%A9|%A3%A8|%A1%AD|%A3%A4|%A1%A4|%A3%A1|%E3%80%82|%EF%BC%81|%EF%BC%8C|%EF%BC%9B|%EF%BC%9F|%EF%BC%9A|%E3%80%81|%E2%80%A6%E2%80%A6|%E2%80%9D|%E2%80%9C|%E2%80%98|%E2%80%99|%EF%BD%9E|%EF%BC%8E|%EF%BC%88)+/", ' ', $text);
    $text = urldecode($text);
    return trim($text);
}
```