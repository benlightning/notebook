# 倒计时按钮设置

```javascript
var wait = 60;
function time(o) {
    if (wait <= 0) {
        o.removeAttribute("disabled");
        o.className = 'default';
        o.innerText = "获取验证码";
        wait = 60;
    } else {
        o.setAttribute("disabled", true);
        o.innerText = "重新发送(" + wait + ")";
        o.className = 'disabled';
        wait--;
        setTimeout(function () {
            time(o)
        }, 1000);
    }
}
```