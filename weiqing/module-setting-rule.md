## module.php文件的内容，设置回复规则和参数设置

```
defined('IN_IA') or exit('Access Denied');

class xxxModule extends WeModule {

    public function fieldsFormDisplay($rid = 0) {
        //要嵌入规则编辑页的自定义内容，这里 $rid 为对应的规则编号，新增时为 0
        global $_W, $_GPC;
        load()->func('tpl');
        if ($rid == 0) {
            $reply = array(
            );
        } else {
            
        }
        include $this->template('form');
    }

    public function fieldsFormValidate($rid = 0) {
        //规则编辑保存时，要进行的数据验证，返回空串表示验证无误，返回其他字符串将呈现为错误提示。这里 $rid 为对应的规则编号，新增时为 0
        return '';
    }

    public function fieldsFormSubmit($rid) {
        //规则验证无误保存入库时执行，这里应该进行自定义字段的保存。这里 $rid 为对应的规则编号
        global $_W, $_GPC;
        
    }

    public function ruleDeleted($rid) {
        //删除规则时调用，这里 $rid 为对应的规则编号
        
    }

    public function settingsDisplay($settings) {
        global $_W, $_GPC;
        $params = array();
        if (empty($_W['isfounder'])) {
            $where = " WHERE `uniacid` IN (SELECT `uniacid` FROM " . tablename('uni_account_users') . " WHERE `uid`=:uid)";
            $params[':uid'] = $_W['uid'];
        }
        $sql = "SELECT * FROM " . tablename('uni_account') . $where;
        $uniaccounts = pdo_fetchall($sql, $params);
        $accounts = array();
        if (!empty($uniaccounts)) {
            foreach ($uniaccounts as $uniaccount) {
                $accountlist = uni_accounts($uniaccount['uniacid']);
                if (!empty($accountlist)) {
                    foreach ($accountlist as $account) {
                        if (!empty($account['key']) && !empty($account['secret']) && in_array($account['level'], array(4))) {
                            $accounts[$account['acid']] = $account['name'];
                        }
                    }
                }
            }
        }
        if (checksubmit()) {
            load()->func('file');
            mkdirs(ATTACHMENT_ROOT . '/cert');
            $r = true;
            $pemname = $settings['pemname'];
            $pemname = isset($pemname) ? $pemname : md5(time());
            if (!empty($_GPC['cert'])) {
                $ret = file_put_contents(ATTACHMENT_ROOT . '/cert/apiclient_cert.pem.' . $pemname, trim($_GPC['cert']));
                $r = $r && $ret;
            }
            if (!empty($_GPC['key'])) {
                $ret = file_put_contents(ATTACHMENT_ROOT . '/cert/apiclient_key.pem.' . $pemname, trim($_GPC['key']));
                $r = $r && $ret;
            }
            if (!empty($_GPC['ca'])) {
                $ret = file_put_contents(ATTACHMENT_ROOT . '/cert/rootca.pem.' . $pemname, trim($_GPC['ca']));
                $r = $r && $ret;
            }
            if (!$r) {
                message('证书保存失败, 请保证 /attachment/cert/ 目录可写');
            }
            $cfg = array(
                'oauth' => $_GPC['oauth'],
                'ip' => $_GPC['ip'],
                'pemname' => $pemname,
            );
            if ($this->saveSettings($cfg)) {
                message('保存成功', 'refresh');
            }
        }
        include $this->template('setting');
    }

}


// site.php中调用配置信息可以使用如下代码
$config = $this->module['config'];

if ($config['oauth'] == 0) { //不借用权限
    $acid = $_W['acid'];
} else {
    $acid = $config['oauth'];
}

```