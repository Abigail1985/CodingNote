
一个服务肯定是有调用方和被调用方的,被调用方被称为服务端,调用方叫做客户端。

grpc 中给服务端取名叫做grpc server，但是客户端不叫grpc client叫做grpc stub，作用就是起到了客户端的作用，用来发起proto请求和接收proto响应。

一句话来说grpc和普通的http请求一样都是cs模型，grpc stub相当于http请求中的client

  
  
作者：Scar  
链接：https://www.zhihu.com/question/384489379/answer/1127190503  
来源：知乎  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。