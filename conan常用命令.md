- [运行 conan_server](https://docs.conan.io/en/latest/uploading_packages/running_your_server.html#running-conan-server)
```
$ conan_server
```
类似的显示结果如下：
```
***********************
Using config: /root/.conan_server/server.conf
Storage: /root/.conan_server/data
Public URL: http://localhost:9300/v1
PORT: 9300
***********************
Bottle v0.12.18 server starting up (using WSGIRefServer())...
Listening on http://0.0.0.0:9300/
Hit Ctrl-C to quit.
```
- 添加远程库
```
$ conan remote add myremote http://IP地址或域名:9300
```
- 在远程库中搜索包
```
$ conan search Poco -r myremote
```
