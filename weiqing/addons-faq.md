## 模块开发中不添加 global $_W 的页面可以不用登录进行访问
#### 
#### manifest.xml中 setting=true 路由到module.php，setting=false路由到site.php中

```php
defined('IN_IA') or exit('Access Denied');

class XxxModule extends WeModule {

	public function welcomeDisplay($menus = array()) {
        //进入模块的默认界面，此方法不存在时进入settingsDisplay方法
        //global $_W, $_GPC;//取消注释时引入的是未登录时的头尾部
		//这里来展示DIY管理界面
		include $this->template('welcome');
	}
    public function settingsDisplay($settings) {

        global $_W, $_GPC;

        if (checksubmit()) {

            $cfg = array(

                'start'=>strtotime($_GPC['date']['start']),

                'end'=>strtotime($_GPC['date']['end']),

                'rules'=>$_GPC['rules'],

            );

            if ($this->saveSettings($cfg)) {

                message('保存成功', 'refresh');

            }

        }

        if(empty($settings['start'])){

            $settings['start'] = time();

        }

        if(empty($settings['end'])){

            $settings['end'] = strtotime('+1 day');

        }

        include $this->template('setting');

    }
}
```