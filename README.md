## 项目技术栈
（详情见[Wiki](https://github.com/sqmax/springboot-project/wiki)）   

### SpringBoot的相关特性

* SpringBoot+JPA
* SpringBoot+Redis
* SpringBoot+

### 微信相关特征

* 微信支付
* 微信登陆
* 微信模板消息推送
* 使用微信相关的开源SDK

### 利用Redis应用分布式Session和锁

### 开发环境及工具
IDEA   
Maven   
Git   
MySQL
Redis                
cosntos虚拟机部署卖家端的前端                                              
Postman模拟微信订单创建订单
Fiddler对手机请求抓包    
Natapp内网穿透                                                       

## 项目演示

### 卖家端（PC端）
浏览器输入授权路径,进入微信扫码登陆系统页面         

![](https://github.com/sqmax/springboot-project/blob/blog/pic/24.PNG)                                                         

登陆后，从左侧导航栏可以看到有四项【订单】，【商品】，【类目】，【登出】，右侧是卖家管理系统的首页，也即【订单】界面。   

![](https://github.com/sqmax/springboot-project/blob/blog/pic/25.PNG)   

 对每项订单有【取消】和查看订单【详情】操作。
 
 ![](https://github.com/sqmax/springboot-project/blob/blog/pic/28.PNG)
 
 我们可以选择完结订单或取消该订单。       

【商品】和【商品类目】下均有两项操作：【列表】【新增】。      
下面以【商品】栏为例演示。     
点击商品->列表可以查看商品的详情，可以看到对每件商品又有【修改】和【上架】/【下架】操作 。       

![](https://github.com/sqmax/springboot-project/blob/blog/pic/26.PNG)

点击商品->新增来新增商品        
 ![](https://github.com/sqmax/springboot-project/blob/blog/pic/23.PNG)     
 
### 买家端
买家端是基于微信公众号的点餐app。      

![](https://github.com/sqmax/springboot-project/blob/blog/pic/28.jpg)

选购好商品后就可以去结算。

![](https://github.com/sqmax/springboot-project/blob/blog/pic/30.jpg)

结算完成，可以看到一条微信支付凭证消息。

![](https://github.com/sqmax/springboot-project/blob/blog/pic/31.jpg)

可以选择查看账单。

![](https://github.com/sqmax/springboot-project/blob/blog/pic/32.jpg)

### 买家端和卖家端的通信
因为我是借用的微信公众账号，买家端和卖家端不能连调，我这里用Postman这个工具，发送一条post请求，来模拟微信下单。这时卖家端首页，即【订单】页面就会弹出一个窗口，并播放音乐。   

![](https://github.com/sqmax/springboot-project/blob/blog/pic/27.PNG)  

点击关闭按钮，买家微信端会收到一个微信模板消息。      

![](https://github.com/sqmax/springboot-project/blob/blog/pic/29.jpg)


