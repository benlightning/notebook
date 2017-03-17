# 使用session_set_save_handler对session进行自定义操作（如添加session存入到数据库的操作）

```php
/*
例如在微信开发中可以使用openid来生成sessionid
$sessionid = md5($openid);
session_id($sessionid);
mySession::start($attr);
$_SESSION['openid'] = $_W['openid'];
mySession::$expire = 3600;//过期时间一小时
 */
class mySession {
	
	public static $attr;
	
	public static $expire;

	public static function start($attr, $expire = 3600) {
        mySession::$attr = $attr;
        mySession::$expire = $expire;
        $sess = new mySession();
        session_set_save_handler(
            array(&$sess, 'open'),
            array(&$sess, 'close'),
            array(&$sess, 'read'),
            array(&$sess, 'write'),
            array(&$sess, 'destroy'),
            array(&$sess, 'gc')
        );
        register_shutdown_function('session_write_close');//可实现当程序执行完成后执行的函数
		session_start();
	}

	public function open() {
		return true;
	}

	public function close() {
		return true;
	}

	
	public function read($sessionid) {
		$sql = 'SELECT * FROM ' . tablename('sessions') . ' WHERE `sessid`=:sessid AND `expiretime`>:time';
		$params = array();
		$params[':sessid'] = $sessionid;
		$params[':time'] = TIMESTAMP;
		$row = pdo_fetch($sql, $params);
		if(is_array($row) && !empty($row['data'])) {
			return $row['data'];
		}
		return false;
	}

	
	public function write($sessionid, $data) {
		$row = array();
		$row['sessid'] = $sessionid;
		$row['data'] = $data;//session存储的序列化数据
		$row['expiretime'] = TIMESTAMP + WeSession::$expire;
		return pdo_insert('sessions', $row, true) >= 1;
	}

	
	public function destroy($sessionid) {
		$row = array();
		$row['sessid'] = $sessionid;

		return pdo_delete('sessions', $row) == 1;
	}

	
	public function gc($expire) {
		$sql = 'DELETE FROM ' . tablename('sessions') . ' WHERE `expiretime`<:expire';

		return pdo_query($sql, array(':expire' => TIMESTAMP)) == 1;
	}
}
```