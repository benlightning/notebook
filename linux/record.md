# 常用使用记录（持续增加）

```
#开机执行脚本，命令写入/etc/rc.local即可（需要root权限）
vim /etc/rc.local

# 添加到环境变量
export PATH=$PATH:/dir/bin  #重启机器后会失效
# 把上面的命令写入 /etc/profile 重启无碍
vi /etc/profile
source /etc/profile
```