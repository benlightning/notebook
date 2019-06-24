## manifest.xml记录

```xml
<application setting="false">...</application><!-- setting来控制是否显示 核心功能设置-参数设置 的显示 -->
<platform>
    <subscribes>
        <!-- 所有模块订阅事件，对应receiver.php,用于处理接收的消息 -->
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
        <message type="merchant_order" />
        <message type="text" />
    </subscribes>
    <handles>
        <!-- 所有模块订阅事件，对应processor.php，用于处理应答响应 -->
        <message type="voice" />
        <message type="text" />
        <message type="subscribe" />
        <message type="qr" />
    </handles>
    <rule embed="true" /><!-- 用于控制 核心功能设置-回复规则列表 的显示 -->
    <card embed="false" /><!-- 没使用过，可能没用吧 -->
</platform>
 <bindings><!-- 业务功能菜单 -->
    <rule>
        <entry title="规则记录扩展" do="list" state="" direct="false" />
    </rule>
    <menu>
        <entry title="菜单名" do="list" state="" direct="false" />
    </menu>
    <cover>
        <entry title="便利店首页" do="store" state="" direct="true" />
    </cover>
    <!-- 以下是app端微网站菜单部分（不需要网站功能时基本无用）-->
    <home>
        <entry title="便利店首页" do="store" state="" direct="true" />
    </home>
    <profile>
        <entry title="我的订单" do="orders" state="" direct="false" />
    </profile>
    <shortcut>
        <entry title="便利店订单" do="orders" state="" direct="false" />
    </shortcut>
</bindings>
    
```