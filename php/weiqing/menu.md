# 微擎目录结构

```text
root
├─ addons  ………………………………………… 【模块安装目录】 (意为附加组件)
│  ├─ business  …………………………………… 模块的名称  (示例)
│  │  ├─ images                             建议 css 文件也放此目录.
│  │  ├─ template                           模板目录
│  │  │  ├─ mobile                         APP 端模板目录
│  │  │  │  └─ ... *.html                 APP 端模板文件
│  │  │  └─ ... *.html                     Web 端模板文件
│  │  ├─ inc                                引用的 php 文件目录
│  │  │  ├─ mobile                         Mobile端目录
│  │  │  │  ├─ xxx.inc.php                微站入口
│  │  │  │  └─ ...                        .
│  │  │  └─ web                            Web端模板目录
│  │  │      ├─ xxx.inc.php                微站管理入口
│  │  │      └─ ...                        .
│  │  ├─ icon.jpg                           模块图标
│  │  ├─ preview.jpg                        模块预览
│  │  ├─ manifest.xml                       安装清单
│  │  ├─ module.php                         模块设置
│  │  ├─ processor.php                      消息处理
│  │  ├─ receiver.php                       消息订阅
│  │  └─ site.php                           微站页面
│  └─ ... …………………………………………… 其他扩展模块
├─ api ………………………………………………… .
│  └─ uc.php                                 .
├─ attachment  ……………………………………… 附件文件夹
│  ├─ audios  ……………………………………… 音频附件文件夹
│  └─ images  ……………………………………… 图片附件文件夹
│      ├─ global  ………………………………… 系统公共文件夹
│      │  └─ 上传年份
│      │        └─上传月份
│      │             └─ 随机文件名
│      └─ $uniacid ... ……………………………按公众号年月组织的图片文件
├─ app    ……………………………………………【微站(Mobile)入口】
│  ├─ common                                 .
│  │  ├─ bootstrap.app.inc.php              .
│  │  ├─ common.func.php                    .
│  │  └─ template.func.php                  .
│  ├─ resource                               .
│  │  ├─ css                                .
│  │  ├─ fonts                              .
│  │  └─ js                                 .
│  ├─ source                                 控制器
│  ├─ themes                                 微站皮肤
│  │  ├─ default                            系统默认
│  │  └─ ...                                自定义皮肤
│  └─ index.php                              微站入口
├─ data  ……………………………………………… .
│  ├─ config.php.default                     .
│  ├─ config.php                             系统配置
│  └─ db.php                                 .
├─ framework  ………………………………………【微擎系统通用的工具类和方法】
│  ├─ builtin  ……………………………………【微擎内建模块】
│  │  ├─ basic                              文字回复模块
│  │  ├─ core                               会话消息统计
│  │  │  └─ receiver.php                   .
│  │  ├─ cover                              通用封面回复
│  │  ├─ default                            默认回复模块
│  │  ├─ music                              音乐回复模块
│  │  ├─ news                               图文回复模块
│  │  ├─ userapi                            自定义接口回复模块
│  │  └─ ...                                .
│  ├─ class ………………………………………… 系统功能类                            .
│  ├─ function  …………………………………… 系统功能函数
│  ├─ library ……………………………………… 其他函数库
│  ├─ model ………………………………………… 业务功能model
│  ├─ bootstrap.inc.php                      .
│  ├─ const.inc.php                          .
│  └─ version.inc.php                        
├─ payment …………………………………………… 支付
├─ web ……………………………………………… 【后台管理(Web)入口】
│  ├─ common  ……………………………………… 公用文件 
│  │  ├─ bootstrap.sys.inc.php              .
│  │  ├─ common.func.php                    .
│  │  ├─ frames.inc.php                     .
│  │  └─ template.func.php                  .
│  ├─ resource  …………………………………… 资源文件
│  │  ├─ components                         JS组件
│  │  ├─ css                                样式文件
│  │  ├─ fonts                              字体资源
│  │  ├─ images                             图片文件
│  │  └─ js
│  │      ├─ app ………………………………… 系统js功能
│  │      ├─ lib ………………………………… js第三方库文件
│  │      └─ require.js  ……………………… .
│  ├─ source  ……………………………………… 后台管理控制器
│  ├─ themes  ……………………………………… 后台管理视图
│  │  ├─ default ………………………………… 内建后台管理样式
│  │  └─ ... ……………………………………… 定制界面样式
│  └─ index.php                              后台入口
├─ api.php ………………………………………… 【公众平台与微擎系统通信的接口】
├─ index.php ………………………………………… 微擎入口
└─ install.php ……………………………………… 微擎安装程序
```