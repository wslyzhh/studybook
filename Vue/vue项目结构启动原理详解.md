## vue项目结构启动原理详解

------

vue项目启动后调用顺序: index.html → main.js → app.vue → index.js → components/组件

一般项目创建好后会有三个文件：index.html、main.js、app.vue

1.index.html ：所有vue文件都是单页面形式开发，所有vue组件都是通过index.html进行渲染加载。

![img](../image/20180709130318204)

2.main.js：相当于java的入口函数，控制初次启动vue项目要加载的组件。

```
import Vue from 'vue'  // 引入vue
import App from  './App'  // 引入App.vue文件
import router from  './router'  // 引入一段路由配置
Vue.use(C) // 全局方法定义为C
```

el:'#app'这个和index.html中的app组件挂钩。官网解释为：

模板将会替换挂载的元素。

watch：用来监听路由的变换，可以用来定义页面切换时的过渡效果。

![img](../image/20180709131345484)

3.App.vue默认为一个根组件。

![img](../image/20180709132029405)

4.index.js文件
引入组件的代码routes定义时，path为你以后页面间路由跳转的路径。name为import进来的名字,component也为这个名字。

![img](../image/20180709132200140)


其他文件：

-build

-build.js 生产环境构建脚本

-utils.js 构建相关工具方法

-webpack.base.conf.js webpack基础配置

-webpack.dev.conf.js webpack开发环境配置

-webpack.prod.conf.js 生产环境配置

-confg 项目配置

–dev.env.js 开发环境变量

–index.js 项目配置文件

–prod.env.js 生产环境变量

–test.env.js 测试环境变量

-package.json npm包配置文件，里面定义了项目的npm脚本，依赖包等信息

-src 源码目录

–main.js 入口js文件

–app.vue 根组件

–components 公共组件目录

–title.vue