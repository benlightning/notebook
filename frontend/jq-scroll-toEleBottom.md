# 重置页面高度使内容页滚动到弹出页面上方，不遮挡

```javascript
var resizeDialog = function () {
    $('#initBox').css('padding-bottom', $('#popup').height());
    $("#initBox").scrollTop($("#initBox")[0].scrollHeight);
};
```