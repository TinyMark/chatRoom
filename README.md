# 使用Node.js + socket.io构建一个在线聊天室

# 项目初始化：
1. 初始化
~~~ 
$ npm init -y
~~~

2. 构建项目基本目录
基本目录如下
~~~
├─.gitignore
├─package-lock.json
├─package.json
├─README.md
├─server.js
├─index.html
~~~

3. 安装express 及 socket.io
~~~
$ npm i express socket.io -S
~~~

# 服务端
> 进入server.js

代码如下：
~~~ js
// 引入express
var express = require('express');
// 使用express
var app = express();
// 创建http服务器
var http = require('http').createServer(app);
// 在服务器上开启socket.io服务
var io = require('socket.io')(http);

// 访问静态文件
app.use('/node_modules', express.static(__dirname + '/node_modules'));

// 访问主页
app.get('/', function (req, res) {
    res.sendFile(__dirname + '/index.html');
});

// io开始connection时的回调函数
io.on('connection', function (socket) {
    // socket监听 'chat message' 事件,拿到 'msg' 信息
    socket.on('chat message', function (msg) {
        // '发射'信息
        io.emit('chat message', msg);
    });
});

// 监听3000端口
http.listen(3000, function () {
    console.log('http://127.0.0.1:3000');
})
~~~

# 客户端
> 进入 /www/index.html

代码如下:
~~~ html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatRoom</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #F7F7F7;
        }

        h3 {
            text-align: center;
        }

        .chatbox {
            width: 500px;
            height: 500px;
            margin: 0 auto;
            border: 1px solid #CCC;
            background-color: #FFF;
            border-radius: 5px;
        }

        .content {
            height: 350px;
            padding: 20px 40px;
            box-sizing: border-box;
            border-bottom: 1px solid #CCC;
            overflow: scroll;
        }

        .content h5 {
            font-size: 20px;
            margin: 10px 0;
        }

        .content p {
            font-size: 18px;
            margin: 0;
        }

        .other {
            text-align: left;
        }

        .self {
            text-align: right;
        }

        .form {
            height: 150px;
        }

        .form .input {
            height: 110px;
            padding: 10px;
            box-sizing: border-box;
        }

        .form .btn {
            height: 40px;
            box-sizing: border-box;
            border-top: 1px solid #CCC;
        }

        .form textarea {
            display: block;
            width: 100%;
            height: 100%;
            box-sizing: border-box;
            border: none;
            resize: none;
            outline: none;
            font-size: 18px;
            font-family: 'yahei';
        }

        .form input {
            display: block;
            width: 100px;
            height: 30px;
            margin-top: 5px;
            margin-right: 20px;
            float: right;
        }
    </style>
</head>

<body>
    <h3>欢迎来到聊天室</h3>
    <div class="chatbox">
        <!-- 聊天内容 -->
        <div class="content">
            <div id="messages" ng-repeat="msg in messages">
                <!-- <h5>who</h5> -->
                <!-- <p>msg</p> -->
            </div>
        </div>

        <!-- 表单 -->
        <div class="form">
            <form>
                <!-- 输入框 -->
                <div class="input">
                    <textarea id="msg"></textarea>
                </div>
                <!-- 按钮 -->
                <div class="btn">
                    <input type="submit" value="发送">
                </div>
            </form>
        </div>
    </div>
    <script src='/node_modules/jquery/dist/jquery.min.js'></script>
    <script src='/socket.io/socket.io.js'></script>
    <script>
        $(function () {
            var socket = io();
            $('form').submit(function () {
                event.preventDefault();
                socket.emit('chat message', $('#msg').val());
                $('#msg').val('');
                return false;
            });
            socket.on('chat message', function (msg) {
                $('#messages').append($('<p>').text(msg));
                $('.content').scrollTop(1000000);
            });
        })
    </script>
</body>

</html>
~~~