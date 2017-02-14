# h5 图片上传预览 异步提交

```javascript
//建立一個可存取到該file的url
function getObjectURL(file) {
    var url = null;
    if (window.createObjectURL != undefined) { // basic
        url = window.createObjectURL(file);
    } else if (window.URL != undefined) { // mozilla(firefox)
        url = window.URL.createObjectURL(file);
    } else if (window.webkitURL != undefined) { // webkit or chrome
        url = window.webkitURL.createObjectURL(file);
    }
    return url;
}
$(document).on('change', '.wishfile', function () {
    var _upload_file = $(this).val().toLowerCase();
    var _upload_file_hz = _upload_file.substr(_upload_file.lastIndexOf('.') + 1);
    var _upload_file = ['jpg', 'png', 'gif', 'jpeg'];
    if ($.inArray(_upload_file_hz, _upload_file) < 0) {
        alert('上传格式不正确');
        $(this).val('');
        return false;
    }
    var objUrl = getObjectURL(this.files[0]);
    //console.log("objUrl = " + objUrl);
    if (objUrl) {
        // img src=objUrl
    }
});
// form要写enctype="multipart/form-data"
var formData = new FormData($("#formid")[0]);
$.ajax({
    url: 'dopost.php',
    type: 'POST',
    data: formData,
    //async: false,  
    cache: false,
    contentType: false,
    processData: false,
    success: function (returndata) {
        if (returndata == 1) {
            alert('上传成功');
        } else {
            alert('上传失败');
        }
    },
    error: function (returndata) {
        alert('something wrong!');
    }
});  
```