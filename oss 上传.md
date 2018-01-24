### 摘要

本文旨在介绍 [Uxcore/Uploader](https://github.com/uxcore/uxcore-uploader)（以下都简称 Upload 组件） 组件如何快速接入 OSS 上传服务（文末有彩蛋）。

OSS 上传分 2 步，获取临时上传 key 和 文件上传。临时 key 有过期时间限制，每次上传之前都要获取。

后端项目要提前接入 OSS 服务，以做好上传准备工作（获取 key 等参数）。

> [官方文档](https://help.aliyun.com/document_detail/32069.html?spm=5176.doc31947.6.737.tweloW)给出了完整的基于 OSS JSSDK 的上传 demo，但是会遇到 2 个问题（后面会具体说明）。

## 1. 获取上传 key 及其他必须参数

OSS 上传文件之前，首先要获取上传地址，会话 key 以及其他上传必须参数，这些参数有过期时间，一般建议设置 30s 即可。

> 获取 key 说白了就是发起一次 AJAX 请求，从服务器获取对应数据。使用 Upload 组件上传的时候，什么时候去获取这个 key 是我们要解决的一个问题；其次是如何将获取到的上传地址等参数设置到当前的上传组件。

Upload 组件借助 `onfileuploadpreparing` 事件获取上传 key

> 上传的完整步骤是：点击上传按钮，选择文件，发起上传。决定什么时机异步获取 key，并且在 key 拿到之后再发起上传是解决问题的关键。Upload 组件是基于 [uxcore/uploadcore](https://github.com/uxcore/uploadcore) 进行 UI 封装的，而 [uxcore/uploadcore](https://github.com/uxcore/uploadcore) 暴露的 `FILE_UPLOAD_PREPARING` 方法是支持 Promise 返回的（可以在正式上传文件之前执行异步事件）， `FILE_UPLOAD_PREPARING` 事件对应 Upload 组件的 `onfileuploadpreparing` 事件。这么一来问题就简单了，我们只需在该事件中返回一个 Promise 封装异步获取 key 的操作即可（手动点赞 [uxcore/uploadcore](https://github.com/uxcore/uploadcore) 对 Promise 的支持 👍）。

Upload 组件 `onfileuploadpreparing` 配置代码如下：

```
{
// 其他配置代码省略 ...
onfileuploadpreparing: (request) => {
    const me = this;
    return new Promise((resolve) => {
        me.dispatch('getOssToken', {
          params: { type: me.uploadType },
          callback: (data) => {
            const c = data;
            if (c) {
              request.setUrl(c.host.replace(/^http[s]?:/, '')); // 设置上传 URL 地址
              request.setParam('OSSAccessKeyId', c.accessid);
              request.setParam('policy', c.policy);
              request.setParam('Signature', c.signature);
              request.setParam('key', c.key);
              request.setParam('success_action_status', '200');
              request.setParam('fileName', c.fileName); // 这里是为了保存 fileName 用于上传完成后读取，非上传必须参数
              resolve();
            }
          },
        });
    });
},
// 其他配置代码省略 ...
}
```

以下几点需要解释：

* 发起 AJAX 请求的方式
    * 这里发起 AJAX 请求使用的是 [Refast 数据流框架（很好用哦）](http://doc.refast.cn/) 配合 [natty-fetch](https://github.com/jias/natty-fetch) 的语法，你可以简单的理解为 JQuery 的 `$.post(url, params, callback)`。
    * [OSS 官方文档](https://help.aliyun.com/document_detail/32069.html?spm=5176.doc31947.6.737.tweloW)给出的 demo 是引入对应的 JSSDK 并使用对应的 `OSS.urllib.request` 方法发起 AJAX 请求。这个方式的问题在于当服务器支持 GZIP 压缩的时候，该 JSSDK 会因为不支持 GZIP 解压缩而报错 `***方法不存在`。这个问题一般情况下并不会发生，除非服务器开启了 GZIP 压缩才会出现，所以这里建议使用你当前项目中发起 AJAX 请求的方式来异步获取数据。
* `request.setUrl(c.host.replace(/^http[s]?:/, ''));` 这里正则去掉上传地址前缀的原因是：当前项目有可能是基于 HTTP 或者 HTTPS，而接口默认返回的上传地址要么是 HTTP，要么是 HTTPS，当两者不对应的时候，浏览器就会相应报错。
* `request.setUrl 和 request.setParam` 方法都是基于 [uxcore/uploadcore](https://github.com/uxcore/uploadcore) 给出的 API，用以设置上传组件对应参数。`OSSAccessKeyId`/`policy`/`Signature`/`key` 都是 OSS 上传必须参数，见[文档](https://help.aliyun.com/document_detail/31988.html?spm=5176.doc31923.6.423.Za0C7o)，`success_action_status` 参数可选，这里是为了方便调试设置的。

## 2. 上传文件

> 该步要做的主要工作即在上传成功之后，将对应数据回填 Upload 组件以展示。这里我们配置 Upload 组件的 `onfileuploadcompleting` 事件来处理数据，将数据格式调整为组件所需格式（OSS 给出的上传地址默认也只是返回 201 或者 200 状态码，不返回其他内容，所以展示数据需要根据 key 去拼接）。

完整示例代码如下：

```
{
  // 其他配置代码省略 ...
  onfileuploadcompleting: (FileResponse) => {
    const imagesExts = ['jpg', 'jpeg', 'gif', 'png', 'bmp', 'svg', 'tiff', 'tif', 'ico', 'jpe', 'svgz', 'pct', 'psp', 'ai', 'psd', 'raw', 'webp'];
    const result = FileResponse;
    let fileName = result.fileRequest.params.params.filter(fileRequrestItem => fileRequrestItem.name === 'fileName');
    fileName = fileName[0].value;
    // 如果是图片则添加预览地址
    const ext = result.fileRequest.file.ext;
    const downloadUrl =
    `${Utils.autoUrl(fetch.downloadFile.generateFamilyCertFileLink)}${fileName}&type=educationExpFile`;
    const previewUrl =
    `${Utils.autoUrl(fetch.downloadFile.generateFamilyCertImagePreview)}${fileName}&type=educationExpFile`;
    result.rawResponse = assign({}, {
      success: true,
      data: {
        url: imagesExts.indexOf(ext) !== -1 ? previewUrl : downloadUrl,
        previewUrl: imagesExts.indexOf(ext) !== -1 ? previewUrl : downloadUrl,
        downloadUrl,
      },
      fileName,
      certFileName: result.fileRequest.file.name,
    });
    return result;
  },
  // 其他配置代码省略 ...
}
```

这段代码的借鉴意义应该要大于实用意义，解释如下：

* 在 `onfileuploadcompleting` 事件中将上传成功后返回的数据拼接为 Upload 组件需要的格式。
* 识别上传文件为图片的时候，给出预览地址，可以方便用户预览。下载按钮使用的是 `<a download></a>`, 如果是图片地址的话，会有[部分浏览器兼容问题](http://www.w3school.com.cn/tags/att_a_download.asp)。
* 将最终要提交给服务器的 key 和 文件名保存到 `result.rawresponse` 里，方便 Form 提交的时候获取对应数据。

## 3. 结语
OSS 结合 Upload 组件实现文件上传比传统情况多了几个步骤，但是没关系，有一个组件叫 [uxcore-ehr-uploader-oss](http://gitlab.alibaba-inc.com/uxcore/uxcore-ehr-uploader-oss) 已经把这些封装起来了，只需要简单配置几个 API 就可以开心的对接 OSS 上传了,详细使用请参考 [uxcore-ehr-uploader-oss readme](http://gitlab.alibaba-inc.com/uxcore/uxcore-ehr-uploader-oss)。
（完）


参考文档

* https://help.aliyun.com/document_detail/32069.html?spm=5176.doc31947.6.737.tweloW
* https://help.aliyun.com/document_detail/31988.html?spm=5176.doc31923.6.423.Za0C7o
* https://github.com/uxcore/uploadcore
* https://github.com/uxcore/uxcore-uploader
* http://uxcore.coding.me/


