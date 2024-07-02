---
title: Asp.Net Mvc使用 WebUploader 多图片上传
date: 2014-12-21 19:23
tags: 
- Asp.Net
- WebUploader
- 多图片上传
categories:
- Asp.Net
---
来博客园有一个月了，哈哈。在这里学到了很多东西。今天也来试着分享一下学到的东西。希望能和大家做朋友共同进步。

      最近由于项目需要上传多张图片，对于我这只菜鸟来说，以前上传图片都是直接拖得控件啊，而且还是一次只能传一张。由于现在 项目用的是MVC，像Asp那样 拖控件 是不现实了。在我脑海中立刻就浮现出一个想法，网上一定有插件，哈哈。去网上一搜索，哇哦这么多。在众多的插件中我被百度的WebUploader吸引了。官网上写着:"WebUploader是由Baidu WebFE(FEX)团队开发的一个简单的以HTML5为主，FLASH为辅的现代文件上传组件...”；就是这句里面的"简单"和"现代" 这两个词深深的吸引了我，当然官网提供的Demo也很酷，而且还是开源的，哇咔咔。既然决定用这个了，我就下了一份源代码下来。打开一看，什么gui ，居然是php的。当时的心情你们能理解么？于是我就抱着试一试的心态搜索了一下 "webuploader asp.net" 哈哈居然搜到到Demo了.Demo 是从 eflay 这位前辈的博客上看的。我借用了他的后台方法，由于前面js写的太复杂我没敢用。好了不扯废话了下面就来演示根据WebUploader官方提供的API和前面说的那位前辈的后台方法完成图片上传。

## 下载
-[WebUploader](https://github.com/fex-team/webuploader/releases) 

##  将下载到的压缩包里面的文件复制到自己的项目中   

##  添加引用

``` javascript
 <!--引入Jquery-->
 <script src="~/Script/jquery-1.8.2.min.js"></script>
 <!--引入Css-->
 <link href="~/CSS/webuploader.css" rel="stylesheet" />
 <!--引入Js-->
 <script src="~/Script/webuploader.js"></script>
```
## 准备一个放图片的容器和一个上传按钮
``` html
<div id="fileList"></div> <!--这是存放图片的容器-->
<div class="cp_img_jia" id="filePicker"></div> <!--这是上传按钮-->
```
## 创建Web Uploader实例并监听事件

```javascript
 <script type="text/javascript">
 
     var applicationPath = window.applicationPath === "" ? "" : window.applicationPath || "../../";
     $(function () {
         var $ = jQuery,
         $list = $('#fileList'),
         // 优化retina, 在retina下这个值是2
         ratio = window.devicePixelRatio || 1,
         // 缩略图大小
         thumbnailWidth = 90 * ratio,
         thumbnailHeight = 90 * ratio,
         // Web Uploader实例
         uploader;
         uploader = WebUploader.create({
             // 选完文件后，是否自动上传。
             auto: false,
 
             // swf文件路径
             swf: applicationPath + '/Script/Uploader.swf',
 
             // 文件接收服务端。
             server: applicationPath + '/Home/UpLoadProcess',
 
             // 选择文件的按钮。可选。
             // 内部根据当前运行是创建，可能是input元素，也可能是flash.
             pick: '#filePicker',
 
             //只允许选择图片
             accept: {
                 title: 'Images',
                 extensions: 'gif,jpg,jpeg,bmp,png',
                 mimeTypes: 'image/*'
             }
         });
        
         // 当有文件添加进来的时候
         uploader.on('fileQueued', function (file) {
             var $li = $(
                     '<div id="' + file.id + '" class="cp_img">' +
                         '<img>' +
                     '<div class="cp_img_jian"></div></div>'
                     ),
                 $img = $li.find('img');
 
 
             // $list为容器jQuery实例
             $list.append($li);
 
             // 创建缩略图
             // 如果为非图片文件，可以不用调用此方法。
             // thumbnailWidth x thumbnailHeight 为 100 x 100
             uploader.makeThumb(file, function (error, src) {
                 if (error) {
                     $img.replaceWith('<span>不能预览</span>');
                     return;
                 }
 
                 $img.attr('src', src);
             }, thumbnailWidth, thumbnailHeight);
         });
 
         // 文件上传过程中创建进度条实时显示。
         uploader.on('uploadProgress', function (file, percentage) {
             var $li = $('#' + file.id),
                 $percent = $li.find('.progress span');
 
             // 避免重复创建
             if (!$percent.length) {
                 $percent = $('<p class="progress"><span></span></p>')
                         .appendTo($li)
                         .find('span');
             }
 
             $percent.css('width', percentage * 100 + '%');
         });
 
         // 文件上传成功，给item添加成功class, 用样式标记上传成功。
         uploader.on('uploadSuccess', function (file, response) {
             
             $('#' + file.id).addClass('upload-state-done');
         });
 
         // 文件上传失败，显示上传出错。
         uploader.on('uploadError', function (file) {
             var $li = $('#' + file.id),
                 $error = $li.find('div.error');
 
             // 避免重复创建
             if (!$error.length) {
                 $error = $('<div class="error"></div>').appendTo($li);
             }
 
             $error.text('上传失败');
         });
 
         // 完成上传完了，成功或者失败，先删除进度条。
         uploader.on('uploadComplete', function (file) {
             $('#' + file.id).find('.progress').remove();
         });
 
         //所有文件上传完毕
         uploader.on("uploadFinished", function ()
         {
            //提交表单
 
         });
 
         //开始上传
         $("#ctlBtn").click(function () {
             uploader.upload();
 
         });
 
         //显示删除按钮
         $(".cp_img").live("mouseover", function ()
         {
             $(this).children(".cp_img_jian").css('display', 'block');
 
         });
         //隐藏删除按钮
         $(".cp_img").live("mouseout", function () {
             $(this).children(".cp_img_jian").css('display', 'none');
 
         });
         //执行删除方法
         $list.on("click", ".cp_img_jian", function ()
         {
             var Id = $(this).parent().attr("id");
             uploader.removeFile(uploader.getFile(Id,true));
             $(this).parent().remove();
         });
       
     });
 
 
 </script>
```
## 在Controller里新建一个Action用于保存图片并返回图片路径（这方法是 eflay 前辈博客上说的）

```C#
 public ActionResult UpLoadProcess(string id, string name, string type, string lastModifiedDate, int size, HttpPostedFileBase file)
        {
            string filePathName = string.Empty;

            string localPath = Path.Combine(HttpRuntime.AppDomainAppPath, "Upload");
            if (Request.Files.Count == 0)
            {
                return Json(new { jsonrpc = 2.0, error = new { code = 102, message = "保存失败" }, id = "id" });
            }

            string ex = Path.GetExtension(file.FileName);
            filePathName = Guid.NewGuid().ToString("N") + ex;
            if (!System.IO.Directory.Exists(localPath))
            {
                System.IO.Directory.CreateDirectory(localPath);
            }
            file.SaveAs(Path.Combine(localPath, filePathName));

            return Json(new
            {
                jsonrpc = "2.0",
                id = id,
                filePath = "/Upload/" + filePathName
            });
        
        }
```
这样就大功告成了。由于是第一次写博客，里面如果有写的不详细或不对的地方，欢迎大家指点。希望能和大家一起进步。

Demo下载地址http://pan.baidu.com/s/1hqqvB0o