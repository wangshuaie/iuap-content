# WebService服务开发

## 7.2.1 WebService简介
Apache CXF 是一个开源的 Services 框架，CXF 利用 Frontend 编程 API 来构建和开发 Services ，像 JAX-WS 。这些 Services 可以支持多种协议，比如：SOAP、XML/HTTP、RESTful HTTP 或者 CORBA ，并且可以在多种传输协议上运行，比如：HTTP、JMS 或者 JBI，CXF 大大简化了 Services 的创建，同时它继承了XFire传统，可以和 Spring 进行无缝集成。
	iUAP平台采用cxf支持对WebService的开发，使可以方便的定义和发布soap 协议的webservice，也可以发布类似RestFul服务方式的webservice。
## 7.2.2 WebService配置
（1）maven配置
 
（2）配置cxf，修改web.xml，加入cxf相关配置。
 
（3）引入spring和cxf集成的配置文件
 
## 7.2.3 WebService使用
（1）soap协议的webservice
编写webservice的接口类和实现，使用@WebService、@WebMethod注解声明接口和方法，示例如下：
 
编写实现类，实现方法：
 
配置applicationContext-soap-server.xml：
 
启动工程后，可以在浏览器中查看WebService的描述：
http://localhost:8080/example_iuap_webservice/cxf/soap/SoapWebServiceDemo?wsdl

 
	编写测试用例，测试方法和返回结果如下格式：
 
 
（2）restful格式的webservice
编写服务类，使用@Path注解声明服务和指定地址映射，使用@Produces注解设置内容返回的格式。
 
    配置applicationContext-jaxrs-server.xml，指定相关配置：
 

启动工程后，在浏览器中可以查看服务的描述文件，描述文件对应的浏览器地址为：http://localhost:8080/example_iuap_webservice/cxf/jaxrs?_wadl
 
编写测试用例，如下：
 
