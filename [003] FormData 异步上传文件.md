###DOM
```
<input type="file" id="file">
```
###一、创建FormData放入待上传文件
```
//准备FormData对象
var formData = new FormData(),
    uploadFile = document.getElementById('file');
    
//将文件放入FormData对象中    
formData.append('file', uploadFile.files[0]);
```
###二、通过xhr发送FormData数据到服务器，实现文件上传
```
//创建xhr对象
var xhr = new XMLHttpRequest();

//监听文件上传进度
xhr.upload.onprogress = function(evt){
    //lengthComputabel: 文件长度是否可计算
    if(evt.lengthComputable){
        //evt.loaded: 已下载的字节数
        //evt.total: 文件总字节数
        var percent = Math.round(evt.loaded*100/evt.total);
        console.log(percent);
    }
}

//监听文件传输开始    
xhr.onloadstart = function(evt）{
        xhr.abort() //终止上传
}

//监听ajax成功完成事件
xhr.onload = function(evt){
    ...
}

//监听ajax错误事件    
xhr.onerror = function(evt){
    ...
}

//监听ajax被中止事件
xhr.onabort = function(evt){
    ...
}

//监听传输结束事件: 不管成功或者失败都会触发
xhr.onloaded = function(evt){
    ...
}   
   
//*发起ajax请求数据
xhr.open('POST', '/url', true);
xhr.send(formData);

```

来源 http://www.jikexueyuan.com/course/2224_3.html?ss=1
