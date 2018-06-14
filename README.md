# uploadImg
图片压缩及多张上传
========
对近期工作中这部分功能实现以及从中遇到的问题做一次整理和汇总，之后会根据工作中的需求或实际情况做不定期的更新。
本人学疏尚浅，如我写的哪部分有问题，欢迎指正，感谢！

### 第一种方式：H5 FormData上传
FormData对象用以将数据编译成键值对，以便用XMLHttpRequest来发送数据。其主要用于发送表单数据，但亦可用于发送带键数据(keyed data)，而独立于表单使用。
这里主要介绍用FormData上传图片。<br/>
主要步骤：<br/>
1、用input获取文件
```<input class="file" type="file" accept="image/*" multiple>```<br/>
accept="image/*"：可调用手机相册或相机。<br/>
测试发现：<br/>
>1）H5页面中android和ios都可调用<br/>
2）安卓app嵌入H5的页面，安卓手机不支持H5自己调用相机或视频，只能调用相册，查了资料，说需要app提供方法才可以，另苹果app中测试没有问题<br/>
　以下是针对不同的需求可在input中设置不同的属性：<br/>
　　只调用相机：```<input type="file" accept="image/*" capture="camera">```<br/>
　　只调用视频：```<input type="file" accept="video/*" capture="camcorder">```<br/>
　　可调用相机或视频：```<input type="file" accept="audio/*" capture="microphone">```<br/>
　multiple:可选择多张上传<br/>

2、创建FormData对象<br/>
```var formData=new FormData();```

3、用fileReader将图片内容转换为Data URL(base64字符串)赋值给img.src<br/>
 （Data URL是一种将图片“嵌入”到HTML中的方法）<br/>

```var reader = new FileReader();  ```<br/>
``` reader.readAsDataURL() ```<br/>
reder.result就是文件转换为base64后的字符串，可以用这个base64的字符串长度来大致判断文件大小。

将压缩后的图片用```canvas.toDataURL(type, encoderOptions)```方法转换为Data URL<br/>
type:设置图片格式，默认为 image/png;如果指定的类型不被支持，则以默认值image/png替代<br/>
encoderOptions :图片质量 0 ~ 1 区间内选择。如果超出取值范围，将会使用默认值 0.92。其他参数会被忽略。<br/>
注意：dataURL 只有在图片加载完成后才会返回，但我们无法确定图片什么时候完成加载。为确保后续处理任务在成功得到 dataURL 之后执行，可以加一个回调函数`callBack(base64)`


4、将base64转换为二进制文件<br/>

5、将字段及图片信息加入到formdata中<br/>
 ```formData.append(name,blob);```<br/>
 
6、发送Ajax请求，请求成功后，获取返回的图片路径显示到前端<br/>


第二种方式：base64编码上传
-----
需求：在app内嵌入H5页面<br/>
使用原因：安卓app不兼容FormData时使用<br/>
同FormData步骤，区别在于不用将压缩后的base64位转换为二进制文件，直接发送请求给后端，由后端去负责解析
