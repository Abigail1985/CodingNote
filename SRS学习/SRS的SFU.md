[https://mp.weixin.qq.com/s/xWe6f9WRhtwnpJQ8SO0Eeg](https://mp.weixin.qq.com/s/xWe6f9WRhtwnpJQ8SO0Eeg)

[https://mp.weixin.qq.com/s?__biz=MzA4NTQ3MzQ5OA==&mid=2649004546&idx=1&sn=04453d2b06c734565f330592d5fee22d&chksm=87c75bb7b0b0d2a19d89a35b2829424ac3099923272cea97ff451313c7918ec7c87dc78f0118&cur_album_id=1683217451712299009&scene=189#wechat_redirect](https://mp.weixin.qq.com/s?__biz=MzA4NTQ3MzQ5OA==&mid=2649004546&idx=1&sn=04453d2b06c734565f330592d5fee22d&chksm=87c75bb7b0b0d2a19d89a35b2829424ac3099923272cea97ff451313c7918ec7c87dc78f0118&cur_album_id=1683217451712299009&scene=189#wechat_redirect)


还有一种方案，是可以将RTC的流送到MCU，比如SRS对接OWT，这样可以获得更好的质量和体验，适合有研发能力的团队。

[https://mp.weixin.qq.com/s?__biz=MzA4NTQ3MzQ5OA==&mid=2649004562&idx=1&sn=d7e812a2ad95cbf65f0ff42cddd3735e&chksm=87c75ba7b0b0d2b13cc7645c1ce1f91219ba784a7cf64310c7271a29d5fa8614e81def3c33ae&cur_album_id=1683217451712299009&scene=189#wechat_redirect](https://mp.weixin.qq.com/s?__biz=MzA4NTQ3MzQ5OA==&mid=2649004562&idx=1&sn=d7e812a2ad95cbf65f0ff42cddd3735e&chksm=87c75ba7b0b0d2b13cc7645c1ce1f91219ba784a7cf64310c7271a29d5fa8614e81def3c33ae&cur_album_id=1683217451712299009&scene=189#wechat_redirect)


## SFU: One to One

SRS早就具备了SFU的能力，比如一对一通话、[多人通话](https://ossrs.net/lts/zh-cn/docs/v4/doc/webrtc#sfu-video-room)、[直播连麦](https://ossrs.net/lts/zh-cn/docs/v4/doc/webrtc#room-to-live)等等。在沟通中，一对一是常用而且典型的场景， 让我们一起来看看如何用SRS做直播和RTC一体化的一对一通话。

> 下面以Docker中运行DEMO为例子，若希望从代码编译，请设置好对应的环境变量和启动命令。

本机启动SRS(参考[usage](https://github.com/ossrs/srs/tree/4.0release#usage))，例如：

```
export CANDIDATE="192.168.1.10"docker run --rm --env CANDIDATE=$CANDIDATE \  -p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \  registry.cn-hangzhou.aliyuncs.com/ossrs/srs:4 \  objs/srs -c conf/rtc.conf
```

> Note: 请将CANDIDATE设置为服务器的外网地址，详细请阅读[WebRTC: CANDIDATE](https://ossrs.net/lts/zh-cn/docs/v4/doc/webrtc#config-candidate)。

> Note: More images and version is [here](https://cr.console.aliyun.com/repository/cn-hangzhou/ossrs/srs/images).

> Note: Demo的H5页面，是在SRS镜像中的。

本机启动信令(参考[usage](https://github.com/ossrs/signaling#usage))，例如：

```
docker run --rm -p 1989:1989 registry.cn-hangzhou.aliyuncs.com/ossrs/signaling:1
```

> Note: More images and version is [here](https://cr.console.aliyun.com/repository/cn-hangzhou/ossrs/signaling/images).

启动[httpx-static](https://github.com/ossrs/go-oryx/tree/develop/httpx-static#usage)，转换HTTPS和WSS协议：

```
export CANDIDATE="192.168.1.10"docker run --rm -p 80:80 -p 443:443 registry.cn-hangzhou.aliyuncs.com/ossrs/httpx:1 \    ./bin/httpx-static -http 80 -https 443 -ssk ./etc/server.key -ssc ./etc/server.crt \          -proxy http://$CANDIDATE:1989/sig -proxy http://$CANDIDATE:1985/rtc \          -proxy http://$CANDIDATE:8080/
```

> Note: 请将CANDIDATE设置为服务器的外网地址，详细请阅读[WebRTC: CANDIDATE](https://ossrs.net/lts/zh-cn/docs/v4/doc/webrtc#config-candidate)。

本机(localhost)可以直接打开[http://localhost/demos/one2one.html?autostart=true](http://localhost/demos/one2one.html?autostart=true)。

若非本机，则可以打开[https://192.168.3.6/demos/one2one.html?autostart=true](https://192.168.3.6/demos/one2one.html?autostart=true)。

> 注意：自签名证书，在空白处输入`thisisunsafe`（注意没空格）。

## SFU: Video Room

SRS支持多人通话的SFU能力，请参考[一对一通话](https://ossrs.net/lts/zh-cn/docs/v4/doc/webrtc#sfu-one-to-one)搭建环境，然后访问页面：

本机(localhost)可以直接打开[http://localhost/demos/room.html?autostart=true](http://localhost/demos/room.html?autostart=true)。

若非本机，则可以打开[https://192.168.3.6/demos/room.html?autostart=true](https://192.168.3.6/demos/room.html?autostart=true)。

> 注意：自签名证书，在空白处输入`thisisunsafe`（注意没空格）。

若需要会议转直播，请参考[RTC转直播](https://ossrs.net/lts/zh-cn/docs/v4/doc/webrtc#room-to-live)。