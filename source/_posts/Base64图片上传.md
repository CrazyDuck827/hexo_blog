---
title: Base64图片上传
date: 2019-09-17 11:30:39
toc: true
categories: 
    - js
tags:
    - js
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/sdXEYmltC5WiIAjL4S0tQv7PGqlk11xk.png
---

# base64图片上传

### web部分

```html
<img name="img" id="Image1" src="">
<input class="imgFile" type="file">
```

### js部分

```javascript
// 实时预览
$(".imgFile").change(function () {
        var file = $(".imgFile").get(0).files[0];
        var reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onloadend = function () {
            $("#Image1").attr("src", reader.result);
        }
    });
// ajax上传
$("#register2").click(function () {
        var IMG_BASE = $("#Image1").attr("src");//要上传的图片的base64编码
        var IMG_ROUTE = $(".imgFile").val();//获取上传图片路径，为获取图片类型使用
        var IMG_ENDFOUR = IMG_ROUTE.substr(IMG_ROUTE.length - 4, 4);//截取路径后四位，判断图片类型
        var IMG_FOMATE = "jpeg"; //图片类型***
        if (IMG_ENDFOUR.trim() == ".jpg")
            IMG_FOMATE = "jpg";
        else if (IMG_ENDFOUR.trim() == ".png")
            IMG_FOMATE = "png";
        else if (IMG_ENDFOUR.trim() == ".bmp")
            IMG_FOMATE = "bmp";
        //图片正式开始上传
        $.ajax({
            type: "post",
            url: "server/head.ashx",
            data: { 'imgBase': IMG_BASE, 'imgFormat': IMG_FOMATE },
            dataType: "text",
            success: function (data) {
                //data为返回的图片路径
            }
        });
    });
```

### php部分

```php
class Base64_Upload
{
    private $config = ROOT_PATH . 'public' . DS . 'upload';

    /**
     * @param $base64_arr  base64格式图片
     * @param array $allow_type  允许上传的文件类型
     * @return array|bool
     */
    function upload($base64_arr, $allow_type=['jpg', 'jpeg', 'png']) {
        if (empty($base64_arr) || !is_array($base64_arr)) return false;
        $flag = true;
        $fileArr = [];
        $base64_imgs = $base64_arr;
        //类型错误中断
        foreach ($base64_imgs as $k => $base64) {
            $imgType = $this->getImgType($base64, '/', ';');
            if (!in_array($imgType, $allow_type)) {
                $flag = false;
                break;
            }
//            list($width, $height)=getimagesize($base64);
//            if(($width/$height) != 1){
//                $flag = false;
//                break;
//            }
        }
        if ($flag) {
            $path = $this->config;
            if(!is_dir($path)){
                if(!@mkdir($path)){
                    return false;
                }
            }
            foreach ($base64_imgs as $k => $base64) {
                $imgType = $this->getImgType($base64, '/', ';');
                $filename = md5(uniqid(mt_rand(), true)) . '.' . $imgType;
                $filePath = $path.'/'.$filename; //路径加名字
                $img = base64_decode($this->getBase64Content($base64));
                file_put_contents($filePath, $img); //base64的图片压缩到文件里
                $rs = (new AliModel())->OssUploadFile($filePath);
                if ($rs['code'] == 1) $fileArr[] = $rs['result']['file_path'];
                unlink($filePath);
            }
            return $fileArr;
        } else {
            return false;
        }
    }

    /**
     * @param $base64Str
     * @param $start
     * @param $end
     * @return bool|string
     * 获取base64图片类型
     */
    private function getImgType($base64Str, $start, $end) {
        return substr($base64Str, strlen($start)+strpos($base64Str, $start),(strlen($base64Str) - strpos($base64Str, $end))*(-1));
    }

    /**
     * @param $base64Str
     * @return bool|string
     * 获取base64图片内容
     */
    private function getBase64Content($base64Str) {
        return substr($base64Str, strpos($base64Str, ',')+1);
    }
}
```

### 附录

附实时预览图片插件：[webuploader](http://fex.baidu.com/webuploader/)    

### 参考资料

[JQuery input file 上传图片](https://www.cnblogs.com/yangchuanqi/p/8477957.html)
