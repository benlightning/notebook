# jquery简易表单验证

```javascript
(function ($, win) {
    dataType = {
        "*": /[\w\W]+/,
        "*6-16": /^[\w\W]{6,16}$/,
        "n": /^\d+$/,
        "n6-16": /^\d{6,16}$/,
        "s": /^[\u4E00-\u9FA5\uf900-\ufa2d\w\.\s]+$/,
        "s6-18": /^[\u4E00-\u9FA5\uf900-\ufa2d\w\.\s]{6,18}$/,
        "p": /^[0-9]{6}$/,
        "m": /^(1[3|4|5|7|8]{1}\d{9})$/,
        "e": /^\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/,
        "url": /^(\w+:\/\/)?\w+(\.\w+)+.*$/,
        "qq": /[1-9][0-9]{4,14}$/,
        "str": /[a-zA-Z]/,
        "str2-20": /[a-zA-Z]{2,20}/,
        "num": /^(?:10|[1-9])$/
    };
    valid = function () {
        var ret = true;
        $(document).find("*[data-rule]").each(function () {
            if (!dataType[$(this).attr('data-rule')].test($(this).val())) {
                ret = false;
                layout($(this).attr('data-msg'));
                return false;
            }
        });
        return ret;
    };
})(jQuery, window);
```