---
author: jeidee
categories:
- node.js
date: '2015-02-26'
guid: https://erlnote.wordpress.com/?p=261
id: 261
permalink: /2015/02/26/node-js-jquery-fileupload/
tags:
- file upload
- jquery
title: node.js + jquery fileupload
url: /2015/02/26/node-js-jquery-fileupload
---

jQuery를 사용해 파일 업로드 처리를 쉽게 할 수 있는 방법을 찾아 보자.

## client

우선 다음 링크에서 jQuery Form Plugin(jquery.form.js)을 다운로드 받는다.

http://jquery.malsup.com/form/

js 디렉토리에 위치한다고 가정하고 다음과 같이 스크립트를 포함시켜 준다.
  
jquery는 bower를 사용해 install 했다.

```html
  
<script src="/bower_components/jquery/dist/jquery.js"></script>
  
<script src="/js/jquery.form.js"></script>

<form id="form\_upload" name="form\_upload" action="/upload" method="post" enctype="multipart/form-data">
      
<input type="file" id="file" name="file">
      
<button type="submit">업로드</button>
      
<progress class="sr-only"></progress>
  
</form>
  
```

form을 위와 같이 추가하고 progress도 추가해 준다.

```javascript
  
$(document).ready(function() {
         
$('#form_upload').ajaxForm({
              
beforeSubmit:function(data, frm, opt) {
                
$('progress').removeClass('sr-only');
              
},
              
success:function(res, stat){
                  
$('progress').addClass('sr-only');
              
},
              
error:function(){
                  
$('progress').addClass('sr-only');
                  
alert('파일업로드 에러!');
              
}
          
});
  
});
  
```

jquery의 .ready() 함수에 위와 같이 .ajaxFomr() 함수를 추가해 주면 되는데 callback 함수가 각각 의미하는 바는 다음과 같다.

  * beforeSubmit: submit 되기 전에 호출되며, 위 코드에서는 단순히 progress바를 보여주는 역할만 한다.
  * success: 업로드가 완료되면 호출되며, progress바를 다시 숨긴다.
  * error: 파일 업로드가 실패할 경우 호출되면, 에러 메세지박스를 출력한다.

## server

node.js를 사용해 파일업로드의 서버측 코드를 작성한다.

app.js에 다음과 같이 코드를 추가한다.

```javascript
  
var busboy = require('connect-busboy'); //middleware for form/file upload
  
var path = require('path'); //used for file path
  
var fs = require('fs-extra'); //File System &#8211; for file manipulation

app.use(busboy());
  
app.use(bodyParser.json());
  
app.use(bodyParser.urlencoded({extended: false}));

app.post('/upload', function(req, res, next) {
    
var fstream;
    
req.pipe(req.busboy);
    
req.busboy.on('file', function (fieldname, file, filename) {
      
console.log("Uploading: " + filename);
      
var uploadedFile = 'public/upload/' + filename;

fstream = fs.createWriteStream(uploadedFile);
      
file.pipe(fstream);
      
fstream.on('close', function () {
        
console.log("Upload Finished of " + filename);
        
res.redirect('back'); //where to go next
      
});
    
});
  
});
  
```

## 참고

  * [jQuery ajax fileupload](http://roqkffhwk.tistory.com/67)