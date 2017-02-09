## 微擎下使用定时任务，模块下创建cron.php
```
define('IN_SYS', true);
require '../../framework/bootstrap.inc.php';
//require '../../web/common/bootstrap.sys.inc.php';//web项目使用  
//require '../../app/common/bootstrap.sys.inc.php';//mobile项目使用  
global $_W,$_GPC;
// 用于命令行时 无法得知uniacid这些存于cookie中的信息 pdo_get之类的方法都可用
```

* 然后使用linux的 crontab 指定  curl http://xxx/addons/modulename/cron.php?i=n