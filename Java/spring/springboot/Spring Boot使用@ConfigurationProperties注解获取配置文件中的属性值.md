## [Spring Boot使用@ConfigurationProperties注解获取配置文件中的属性值](https://www.cnblogs.com/niwotaxuexiba/p/10849482.html)

![img](https://img2018.cnblogs.com/blog/1304881/201905/1304881-20190511180450340-99615761.png)

注意：这种方式要提供属性的getter/setter方法——

![img](https://img2018.cnblogs.com/blog/1304881/201905/1304881-20190511180552596-519344317.png)

如果idea报错，提示没有相应的执行器，就需要在maven中添加：

（虽然不配置代码也能正常运行，作用在下面会说明）

![img](https://img2018.cnblogs.com/blog/1304881/201905/1304881-20190511180930590-495865600.png)

![img](https://img2018.cnblogs.com/blog/1304881/201905/1304881-20190511181023248-854634830.png)

 配置了该执行器后，在yml文件中编辑时，会有代码提示：

（具体提示依据是你在@ConfigurationProperties注解的类中定义的属性）

![img](https://img2018.cnblogs.com/blog/1304881/201905/1304881-20190511181259565-417660067.png)