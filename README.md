# UEditor 前端项目案例
#### 前端项目开发过程中，为了实现在线文本编辑功能，我们引用了UEditor组件。
#### 但是使用其图片文件上传功能时由于前端项目与后端项目不在同一域中，导致了跨域后图片上传与图片在线管理功能失效。
#### 尤其在UEditor中使用了大量iframe框架，使得跨域操作更加难以实现。
#### 在此项目案例中，为了解决跨域问题，我们修改了ueditor.all.js文件的源码。
#### 将图片上传的方式由表单提交模式更改为Ajax异步提交模式，屏蔽了iframe跨域引发的问题。
#### 主要重写了源代码中的 domUtils.on() 函数。

## 项目中使用UEditor时需要注意一下两点：
#### 1.修改ueditor.config.js

<code>
  window.UEDITOR_CONFIG ={ UEDITOR_HOME_URL: URL,serverUrl: "http://localhost:8080/editor/controller.jsp"
</code>

#### 通过 serverUrl 设置服务器统一请求接口路径，使用时改成自己的后端项目地址

#### 2.在引用UEditor组件的页面中需要先手工引入jquery

<code>
  <script src="ueditor/third-party/jquery-1.10.2.js"></script>
</code>  
