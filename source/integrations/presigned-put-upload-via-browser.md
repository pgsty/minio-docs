<a name="upload-files-using-pre-signed-urls-"></a>
# 使用 Pre-signed URL 上传文件 [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io)

使用 pre-signed URL，客户端可以直接将文件上传到兼容 S3 的云存储服务器（S3），而无需向用户暴露 S3 凭证。

本文介绍如何使用 [MinIO JavaScript Library](https://github.com/minio/minio-js) 中的 [`presignedPutObject`](https://silo.pigsty.cc/developers/go/API.html#presignedputobject-ctx-context-context-bucketname-objectname-string-expiry-time-duration-url-url-error) API 生成 pre-signed URL。文中通过一个 JavaScript 示例进行演示：由 Express Node.js 服务器暴露一个用于生成 pre-signed URL 的端点，客户端 Web 应用再使用该 URL 将文件上传到 MinIO Server。

- [使用 Pre-signed URL 上传文件 ](#upload-files-using-pre-signed-urls-)
  - [1. 创建服务端](#createserver)
  - [2. 创建客户端 Web 应用](#createclient)

## <a name="createserver"></a>1. 创建服务端
服务端由一个 [Express](https://expressjs.com) Node.js 服务器组成，并暴露名为 `/presignedUrl` 的端点。该端点使用 `Minio.Client` 对象生成短时有效的 pre-signed URL，用于将文件上传到 MinIO Server。

```js
// In order to use the MinIO JavaScript API to generate the pre-signed URL, begin by instantiating
// a `Minio.Client` object and pass in the values for your server.
// The example below uses values for play.min.io:9000

const Minio = require('minio')

var client = new Minio.Client({
    endPoint: 'play.min.io',
    port: 9000,
    useSSL: true,
    accessKey: 'Q3AM3UQ867SPQQA43P2F',
    secretKey: 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG'
})

// Instantiate an `express` server and expose an endpoint called `/presignedUrl` as a `GET` request that
// accepts a filename through a query parameter called `name`. For the implementation of this endpoint,
// invoke [`presignedPutObject`](https://silo.pigsty.cc/developers/go/API.html#presignedputobject-ctx-context-context-bucketname-objectname-string-expiry-time-duration-url-url-error) 
// on the `Minio.Client` instance to generate a pre-signed URL, and return that URL in the response:

// express is a small HTTP server wrapper, but this works with any HTTP server
const server = require('express')()

server.get('/presignedUrl', (req, res) => {
    client.presignedPutObject('uploads', req.query.name, (err, url) => {
        if (err) throw err
        res.end(url)
    })
})

server.get('/', (req, res) => {
    res.sendFile(__dirname + '/index.html');
})

server.listen(8080)
```

## <a name="createclient"></a>2. 创建客户端 Web 应用
客户端 Web 应用的用户界面包含一个选择器字段，允许用户选择要上传的文件，以及一个用于触发名为 `upload` 的 `onclick` 处理函数的按钮：

```html
<input type="file" id="selector" multiple>
<button onclick="upload()">Upload</button>

<div id="status">No uploads</div>

<script type="text/javascript">
  // `upload` iterates through all files selected and invokes a helper function called `retrieveNewURL`.
  function upload() {
        // Get selected files from the input element.
        var files = document.querySelector("#selector").files;
        for (var i = 0; i < files.length; i++) {
            var file = files[i];
            // Retrieve a URL from our server.
            retrieveNewURL(file, (file, url) => {
                // Upload the file to the server.
                uploadFile(file, url);
            });
        }
    }

    // `retrieveNewURL` accepts the name of the current file and invokes the `/presignedUrl` endpoint to
    // generate a pre-signed URL for use in uploading that file: 
    function retrieveNewURL(file, cb) {
        fetch(`/presignedUrl?name=${file.name}`).then((response) => {
            response.text().then((url) => {
                cb(file, url);
            });
        }).catch((e) => {
            console.error(e);
        });
    }

    // ``uploadFile` accepts the current filename and the pre-signed URL. It then uses `Fetch API`
    // to upload this file to S3 at `play.min.io:9000` using the URL:
    function uploadFile(file, url) {
        if (document.querySelector('#status').innerText === 'No uploads') {
            document.querySelector('#status').innerHTML = '';
        }
        fetch(url, {
            method: 'PUT',
            body: file
        }).then(() => {
            // If multiple files are uploaded, append upload status on the next line.
            document.querySelector('#status').innerHTML += `<br>Uploaded ${file.name}.`;
        }).catch((e) => {
            console.error(e);
        });
    }
</script>
```

**注意：** 本示例使用了 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File)、[QuerySelector API](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector)、[fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 和 [Promise API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。
