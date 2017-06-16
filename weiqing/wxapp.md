## 小程序开发模块(https://www.we7.cc/manual/module:wxapp)
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns="http://www.we7.cc" versionCode="0.6">
    <application setting="false">
        <name><![CDATA[小程序]]></name>
        <identifie><![CDATA[xxx]]></identifie>
        <version><![CDATA[1.0]]></version>
        <type><![CDATA[business]]></type>
        <ability><![CDATA[小程序]]></ability>
        <description><![CDATA[小程序]]></description>
        <author><![CDATA[zlyer]]></author>
        <url><![CDATA[http://bbs.we7.cc/]]></url>
        <wxapp_support><![CDATA[1]]></wxapp_support>
    </application>
    <platform>
        <subscribes>
            <message type="image" />
            <message type="voice" />
            <message type="video" />
            <message type="shortvideo" />
            <message type="location" />
            <message type="link" />
            <message type="subscribe" />
            <message type="qr" />
            <message type="trace" />
            <message type="click" />
            <message type="text" />
        </subscribes>
        <handles>
            <message type="image" />
            <message type="voice" />
            <message type="video" />
            <message type="shortvideo" />
            <message type="location" />
            <message type="link" />
            <message type="subscribe" />
            <message type="qr" />
            <message type="trace" />
            <message type="click" />
            <message type="text" />
        </handles>
        <supports>
            <item type="wxapp" />
        </supports>
        <rule embed="false" />
        <card embed="false" />
    </platform>
    <bindings>
        <menu>
            <entry title="列表" do="index" state="" direct="false" />
        </menu>
        <cover>
            <!--<entry title="入口设置" do="index" state="" direct="true" />-->
        </cover>
    </bindings>
    <permissions>
    </permissions>
    <install><![CDATA[
		]]></install>
    <uninstall><![CDATA[
		]]></uninstall>
    <upgrade><![CDATA[]]></upgrade>
</manifest>
```
## wxapp.php

```php
<?php
defined('IN_IA') or exit('Access Denied');
class xxxModuleWxapp extends WeModuleWxapp
{
    public function doPageIndex()
    {
        global $_W, $_GPC;
    }
}
```