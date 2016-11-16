图片/文件上传
=========

> 简介：这是一个麻雀虽小五脏俱全的文件上传组件，不仅仅只是对图片的上传方案，对普通文件、视频、音频都做了内置支持。不过它目前是单文件上传，还无法一次性上传多文件，但却足以应付许多的项目需求了。

参考 ： 

[http://http://www.layui.com/doc/modules/upload.html](http://www.layui.com/doc/modules/upload.html)


##使用

一切从小试牛刀开始。通常情况下，我们上传文件是借助type为file的input标签来完成的，但非常遗憾的是，它不能很好地与其它表单元素并存，所以我们常常要单独为它做一个业务层面的“异步上传”，即先让图片上传，再和其它表单一起提交保存。下面就是一个非常普通的input，它可以出现你页面的任何位置。

	<input type="file" name="file（可随便定义）" class="layui-upload-file">

另外class="layui-upload-file"不是必须的，她只是会让你的input短暂性隐藏，并且在调用upload方法时不用设置elem。要使得这个input能正常地用起来，你只需要执行下述方法：

	var Upload = require('layui_upload');
	new Upload({
	  url: '上传接口url'
	  ,success: function(res){
	    console.log(res); //上传成功返回值，必须为json格式
	  }
	});   


## 核心方法

	new Upload(options); 

### options是一个对象参数，可支持的key如下表

<table class="site-table">
        <thead>
          <tr>
            <th>参数</th>
            <th>类型</th>
            <th>描述</th>
          </tr> 
        </thead>
        <tbody>
          <tr>
            <td>elem</td>
            <td>string</td>
            <td>指定元素的选择器，默认直接查找class为layui-upload-file的元素</td>
          </tr>
          <tr>
            <td>url</td>
            <td>string</td>
            <td>上传文件的接口</td>
          </tr>
          <tr>
            <td>method</td>
            <td>string</td>
            <td>设置http类型，如：post、get。默认post。也可以直接在input设置lay-method="get"来取代。</td>
          </tr>
          <tr>
            <td>before</td>
            <td>function</td>
            <td>执行上传前的回调</td>
          </tr>
          <tr>
            <td>success</td>
            <td>function</td>
            <td>上传成功后的回调</td>
          </tr>
          <tr>
            <td>type</td>
            <td>string</td>
            <td>设定上传的文件类型，也可以直接在input设置lay-type=""来取代（详细见下文）。</td>
          </tr>
          <tr>
            <td>ext</td>
            <td>string</td>
            <td>自定义可支持的文件扩展名，也可以直接在input设置lay-ext=""来取代（详细见下文）。</td>
          </tr>
          <tr>
            <td>unwrap</td>
            <td>boolean</td>
            <td>是否不改变input的样式风格。默认false</td>
          </tr>
        </tbody>
      </table>


##上传接口

像设置Ajax的url一样，你也需要对文件的上传指定一个接口。该接口所返回的信息必须是一个标准的json格式，你可以任意定义返回的字段。假设你设定了一个url: "/upload"，那么执行上传后，它可以返回如下信息（只要是json即可）

	{
	  "code": 0
	  ,"msg": ""
	  ,"data": {
	    src: "http://cdn.abc.com/123.jpg"
	  }
	}    

你不一定非得返回上述格式，注意：只要是返回json即可。其响应信息会如数传递给success回调。

##执行上传前的回调

触发文件上传后，在它成功上传之前，你肯定想做些什么，譬如loading？没错，before可以帮助你完成这些交互。

	new Upload({
	  url: ''
	  ,before: function(input){
	    //返回的参数item，即为当前的input DOM对象
	    console.log('文件上传中');
	  }
	  ,success: function(res){
	    console.log('上传完毕');
	  }
	}); 

##上传成功后的回调

无论文件是否真的上传成功，只要是经过了服务器，都会进入该回调。并返回两个参数，分别为：响应信息、当前的input对象

	new Upload({
	  url: ''
	  ,success: function(res, input){
	    console.log(res); //如：{"code":0 ,"msg":"","url":"http://cdn.abc.com/123.jpg"'} 
	  }
	}); 

那么你就可以根据返回的res做一些交互分支了。

##设置文件类型

一开始就提到，我们支持各种文件类型的上传。通知设置type后，upload组件将默认对文件类型做校验处理。type所支持的值如下表：

<table class="site-table">
<thead>
  <tr>
    <th>值</th>
    <th>描述</th>
  </tr> 
</thead>
<tbody>
  <tr>
    <td>images</td>
    <td>图片类型。默认，不用设定</td>
  </tr>
  <tr>
    <td>file</td>
    <td>普通文件类型。</td>
  </tr>
  <tr>
    <td>video</td>
    <td>视频文件类型。</td>
  </tr>
  <tr>
    <td>audio</td>
    <td>音频文件类型。</td>
  </tr>
</tbody>
</table>

除了在layui.upload()方法中设置type，你还可以直接在input元素上设置lay-type属性，我们更推荐你这样做。如：

	<input type="file" name="" lay-type="video" class="layui-upload-file">


##自定义支持的文件格式

尽管我们内置了各文件类型的校验，但有的时候，你可能要自己定义你网站所支持上传的文件格式。那么通过设置ext即可实现

	new Upload({
	  url: ''
	  ,ext: 'jpg|png|gif' //那么，就只会支持这三种格式的上传。注意是用|分割。
	  ,success: function(res, input){}
	});  

和type一样，除了在layui.upload()方法中设置，你还可以直接在input元素上设置lay-ext属性，如：

	<input type="file" name="" lay-ext="jpg|png|gif" class="layui-upload-file">

##自定义文本

默认情况下，我们对按钮输出的文本是：上传图片，或上传文件/视频/音频，如果你想定义一个不一样的文本，使用参数title即可，如：

	new Upload({
	  url: ''
	  ,title: '请上传压缩包吧亲'
	  ,ext: 'zip|rar'
	  ,type: 'file'
	  ,success: function(res, input){}
	});     

除了在layui.upload()方法中设置，你还可以直接在input元素上设置lay-title

	<input type="file" name="" lay-ext="zip|rar" lay-type="file" lay-title="请上传一张图片吧亲" class="layui-upload-file"> 

##不重置input风格

我们默认会对你的文件表单框做一个UI的重置，但有时你的上传按钮是实现好了的或者你就是想要原始的文件表单风格，你仅仅只是想要一个上传媒介，而并不想要他的UI被改变。那么你可以通过设置unwrap去做过，它是一个布尔型，当为true时，就不会重置你想要的风格了。


	new Upload({
	  url: ''
	  ,unwrap: true //其实就是不向你的input包裹upload组件的ui元素
	  ,success: function(res, input){}
	}); 










