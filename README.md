# UEditor 前端项目案例
#### 前端项目开发过程中，为了实现在线文本编辑功能，我们引用了UEditor组件。
#### 但是使用其图片文件上传功能时由于前端项目与后端项目不在同一域中，导致了跨域后图片上传与图片在线管理功能失效。
#### 尤其在UEditor中使用了大量iframe框架，使得跨域操作更加难以实现。
#### 在此项目案例中，为了解决跨域问题，我们修改了ueditor.all.js文件的源码。
#### 将图片上传的方式由表单提交模式更改为Ajax异步提交模式，屏蔽了iframe跨域引发的问题。
#### 源代码中的 domUtils.on() 函数被修改如下：
/***********************************************************************************************************************************************************************************************************************/
domUtils.on(input, 'change', function() {
  if(!input.value) return;
  var loadingId = 'loading_' + (+new Date()).toString(36);
  var imageActionUrl = me.getActionUrl(me.getOpt('imageActionName'));
  var allowFiles = me.getOpt('imageAllowFiles');
  me.focus();
  me.execCommand('inserthtml', '<img class="loadingclass" id="' + loadingId + '" src="' + me.options.themePath + me.options.theme +'/images/spacer.gif" title="' + (me.getLang('simpleupload.loading') || '') + '" >');
  if (!me.getOpt('imageActionName')) {
    errorHandler(me.getLang('autoupload.errorLoadConfig'));
    return;
  }
  var filename = input.value,
    fileext = filename ? filename.substr(filename.lastIndexOf('.')):'';
  if (!fileext || (allowFiles && (allowFiles.join('') + '.').indexOf(fileext.toLowerCase() + '.') == -1)) {
    showErrorLoader(me.getLang('simpleupload.exceedTypeError'));
    return;
  }
  var params = utils.serializeParam(me.queryCommandValue('serverparam')) || '';
  var action = utils.formatUrl(imageActionUrl + (imageActionUrl.indexOf('?') == -1 ? '?' : '&') + params);
  var formData = new FormData();
  formData.append("upfile", form[0].files[0] );
  $.ajax({
    url: action,
    type: 'POST',
    cache: false,
    data: formData,
    processData: false,
    contentType: false,
    success: function (data) {
      data = JSON.parse(data);
      var link, loader,
        body = (iframe.contentDocument || iframe.contentWindow.document).body,
        result = body.innerText || body.textContent || '';
      link = me.options.imageUrlPrefix + data.url;

      if(data.state == 'SUCCESS' && data.url) {
        loader = me.document.getElementById(loadingId);
        loader.setAttribute('src', link);
        loader.setAttribute('_src', link);
        loader.setAttribute('title', data.title || '');
        loader.setAttribute('alt', data.original || '');
        loader.removeAttribute('id');
        domUtils.removeClasses(loader, 'loadingclass');
      } else {
        showErrorLoader && showErrorLoader(data.state);
      }
      form.reset();
    }
  });
  function showErrorLoader(title){
    if(loadingId) {
      var loader = me.document.getElementById(loadingId);
      loader && domUtils.remove(loader);
      me.fireEvent('showmessage', {
        'id': loadingId,
        'content': title,
        'type': 'error',
        'timeout': 4000
      });
    }
  }
});
/********************************************************************************************************************************************************************/