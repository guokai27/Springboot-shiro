# 整合Spring Boot API Project Seed+shiro
## shiro 设计思路
### 核心

 	每个登录用户拥有各自的N条权限,比如 文章:查看/编辑/发布/删除
  
###  后端

基于 [RBAC新解](http://globeeip.iteye.com/blog/1236167) . 

通常我们的权限设计都是 用户--角色--权限 ,其中**角色**是我们写代码的人没法控制的,它可以有多条权限,每个用户又可以设计为拥有多个角色.因此如果从角色着手进行权限验证,系统都必须根据用户的配置动起来,非常复杂.

所以我们后台设计的关键点就在于: **后台接口只验证权限,不看角色.**

角色的作用其实只是用来管理分配权限的,真正的验证只验证**权限** ,而不去管你是否是那种角色.体现在代码上就是接口上注解为

```java
@RequiresPermissions("article:add")
```

而不是

```java
@RequiresRoles(value = {"admin","manager","writer"}, logical = Logical.OR) 
```
### 前端

采用了[vueAdmin-template](https://github.com/PanJiaChen/vueAdmin-template) , [ElementUI](https://github.com/ElemeFE/element) , 权限设计思路也是参考了 vueAdmin 的动态路由的设计.

后端负责了接口的安全性,而前端之所以要做权限处理,最主要的目的就是**隐藏掉不具有权限的菜单(路由)和按钮**.

登录系统后,后端返回此用户的权限信息,比如 
```json
 "userPermission":{  
         "menuList":[  
            "role",
            "user",
            "article"
         ],
         "roleId":1,
         "nickname":"超级用户",
         "roleName":"管理员",
         "permissionList":[  
            "article:list",
            "article:add",
            "user:list",
         ],
         "userId":10003
      }
```
根据**menuList**判断给此用户生成哪些路由, 根据**permissionList**判断给用户显示哪些按钮,能请求哪些接口.

### 数据库
最主要的是要有一张本系统内的全部权限明细表,比如下面这样
![权限表](http://img.heeexy.com/permissionDatabase.png)
![权限数据](http://img.heeexy.com/permissionData.png)

如果某用户拥有表格中前五条权限,就可以查出他就拥有article和user两个菜单,至于页面内是否显示(新增)(修改)按钮,就根据他的permissionList来判断.

## 具体实现
有了思路,就可以根据各自的业务进行实现,本项目在此进行了简单的实现,后端代码在back文件夹,前端代码在vue文件夹.前端启动只需
```
npm install --registry=https://registry.npm.taobao.org
npm run dev
```

后端就是常规的shiro配置,前端代码如果看不明白,可以参考[前端权限代码说明](./explain-frontend.md) 

## 分配权限页面效果
![分配权限页面](http://img.heeexy.com/role_permission.png)
## Spring Boot API Project Seed简介如下
![Licence](https://img.shields.io/badge/licence-none-green.svg)
[![GitHub Release](https://img.shields.io/github/release/lihengming/spring-boot-api-project-seed.svg)](https://github.com/lihengming/spring-boot-api-project-seed/releases)
## 简介
Spring Boot API Project Seed 是一个基于Spring Boot & MyBatis的种子项目，用于快速构建中小型API、RESTful API项目，该种子项目已经有过多个真实项目的实践，稳定、简单、快速，使我们摆脱那些重复劳动，专注于业务代码的编写，减少加班。下面是一个简单的使用演示，看如何基于本项目在短短几十秒钟内实现一套简单的API，并运行提供服务。

[![请选择超清](https://raw.githubusercontent.com/lihengming/java-codes/master/shared-resources/github-images/project-example-youku.png)](http://v.youku.com/v_show/id_XMjg1NjYwNDgxNg==.html?spm=a2h3j.8428770.3416059.1)
## 特征&提供
- 最佳实践的项目结构、配置文件、精简的POM（[查看项目结构图](https://github.com/lihengming/java-codes/blob/master/shared-resources/github-images/project-struct.png)）
- 统一响应结果封装及生成工具
- 统一异常处理
- 简单的接口签名认证
- 常用基础方法抽象封装
- 使用Druid Spring Boot Starter 集成Druid数据库连接池与监控
- 使用FastJsonHttpMessageConverter，提高JSON序列化速度
- 集成MyBatis、通用Mapper插件、PageHelper分页插件，实现单表业务零SQL
- 提供代码生成器根据表名生成对应的Model、Mapper、MapperXML、Service、ServiceImpl、Controller等基础代码，其中Controller模板默认提供POST和RESTful两套，根据需求在```CodeGenerator.genController(tableName)```方法中自己选择，默认使用POST模板。代码模板可根据实际项目的需求来扩展，由于每个公司业务都不太一样，所以只提供了一些比较基础、通用的模板，主要是提供一个思路来减少重复代码的编写，我在实际项目的使用中，其实根据公司业务的抽象编写了大量的模板。另外，使用模板也有助于保持团队代码风格的统一
- 另有彩蛋，待你探索
 
## 快速开始
1. 克隆项目
2. 对```test```包内的代码生成器```CodeGenerator```进行配置，主要是JDBC，因为要根据表名来生成代码
3. 如果只是想根据上面的演示来亲自试试的话可以使用```test resources```目录下的```demo-user.sql```，否则忽略该步
3. 输入表名，运行```CodeGenerator.main()```方法，生成基础代码（可能需要刷新项目目录才会出来）
4. 根据业务在基础代码上进行扩展
5. 对开发环境配置文件```application-dev.properties```进行配置，启动项目，Have Fun！
 
## 开发建议
- 表名，建议使用小写，多个单词使用下划线拼接
- Model内成员变量建议与表字段数量对应，如需扩展成员变量（比如连表查询）建议创建DTO，否则需在扩展的成员变量上加```@Transient```注解，详情见[通用Mapper插件文档说明](https://mapperhelper.github.io/docs/2.use/)
- 建议业务失败直接使用```ServiceException("message")```抛出，由统一异常处理器来封装业务失败的响应结果，比如```throw new ServiceException("该手机号已被注册")```，会直接被封装为```{"code":400,"message":"该手机号已被注册"}```返回，无需自己处理，尽情抛出
- 需要工具类的话建议先从```apache-commons-*```和```guava```中找，实在没有再造轮子或引入类库，尽量精简项目
- 开发规范建议遵循阿里巴巴Java开发手册（[最新版下载](https://github.com/lihengming/java-codes/blob/master/shared-resources/%E9%98%BF%E9%87%8C%E5%B7%B4%E5%B7%B4Java%E5%BC%80%E5%8F%91%E6%89%8B%E5%86%8CV1.2.0.pdf))
- 建议在公司内部使用[ShowDoc](https://github.com/star7th/showdoc)、[SpringFox-Swagger2](https://github.com/springfox/springfox) 、[RAP](https://github.com/thx/RAP)等开源项目来编写、管理API文档
 
## 技术选型&文档
- Spring Boot（[查看Spring Boot学习&使用指南](http://www.jianshu.com/p/1a9fd8936bd8)）
- MyBatis（[查看官方中文文档](http://www.mybatis.org/mybatis-3/zh/index.html)）
- MyBatisb通用Mapper插件（[查看官方中文文档](https://mapperhelper.github.io/docs/)）
- MyBatis PageHelper分页插件（[查看官方中文文档](https://pagehelper.github.io/)）
- Druid Spring Boot Starter（[查看官方中文文档](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter/)）
- Fastjson（[查看官方中文文档](https://github.com/Alibaba/fastjson/wiki/%E9%A6%96%E9%A1%B5)）
- 其他略

## License
无，纯粹开源分享，感谢大家 [Star](https://github.com/lihengming/spring-boot-api-project-seed/stargazers) & [Fork](https://github.com/lihengming/spring-boot-api-project-seed/network/members) 的支持。

