###DOM
```
<input type="file" name="image" accept="image/*">
<input type="submit" value="上传图片">
```
###Javascript
- 使用 FileReader 将本地图片解析为base64格式
- 使用 FormData 作为载体将base64图片数据传到后台
```
var fileInput = document.getElementsByTagName('input')[0],
    submitButton = document.getElementsByTagName('input')[1];
var fileReader = new FileReader(),
    imgData;
    
    //获取本地图片
    fileInput.addEventListaner('change', function(){
        var file = fileInput.files[0];
        fileReader.readAsDataURL(file);
    }
    
    //将base64格式的图片数据存到imgData
    fileReader.addEventListener('onload', function(){
        imgData = fileReader.result;
    }
    
    //Ajax上传图片数据到后台
    function uploadImg(){
    
        //FormData保存图片信息
        var formData =  new FormData(),
            xhr = new XMLHttpRequest();
        
        formData.append('imgData', imgData);
        
        xhr.open('POST', '/uploadImage', true);
        xhr.send(formData);
    }
    
    submitButton.addEventListener('click', function(){
        uploadImg();
    }
    
```
###Express
- 使用Multipaty解析表单
```
var express = require('express');
var router = express.Router();
//引入Multiparty解析表单
var multiparty = require('multiparty');
var fs = require('fs');
```
```

router.post('/uploadImage', function(req, res){
	var form = new multiparty.Form();
	form.parse(req, function(err, fields, files){
        //将前台传来的base64数据去掉前缀
		var imgData = fields.imgData[0].replace(/^data:image\/\w+;base64,/, '');

		var dataBuffer = new Buffer(imgData, 'base64');
        //写入文件
		fs.writeFile('public/images/imge.png', dataBuffer, function(err){
			if(err){
				res.send(err);
			}else{
				res.send('保存成功');
			}
		});

	});

});
```
