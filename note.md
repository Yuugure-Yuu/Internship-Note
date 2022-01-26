IDEA代码格式化快捷键：**Ctrl + Alt + L**

​         全局搜索快捷键：**Ctrl + Shift + R**

​         本页搜索快捷键：**Ctrl + F**

云原生

lsof -i 端口占用

## Docker命令

docker run -d --name 容器名 -p 外部映射端口:内部服务端口 -v 宿主机文件路径:docker内部文件路径（文件挂载） --privileged=true --restart=always 镜像名:版本号

docker logs -f [container_id/container_name] 查看容器日志，查找错误信息

docker logs -t --since="2022-01-10T07:49:45" --until "2022-01-10T07:50:00" CONTAINER_ID

ctrl+c退出

docker rm -f [container_id/container_name]

docker inspect [container_name] 查看容器映射路径信息

docker restart [container_id] 重启

```
# 判断容器是否存在
imageId=$(docker ps -q -f name=^/autoapi-test\$)
if [ -n "$imageId" ]; then

  docker stop autoapi-test
  
  docker rm autoapi-test
  
  num=${BUILD_VERSION: 4: 4}
  new_num=$(expr $num - 1)
  newVersion=${BUILD_VERSION: 0: 4}$new_num
  
  docker rmi autoapi-test:${newVersion}
  
fi

cd /home/jenkins/workspace/Auto-Api-test

# 复制jar包到Dockerfile所在的目录
cp /home/jenkins/workspace/Auto-Api-test/target/API_project-0.0.1-SNAPSHOT.jar /home/jenkins/workspace/Auto-Api-test


# 创建镜像
docker build -t autoapi-test:${BUILD_VERSION} .

# 启动容器
docker run -d --name=autoapi-test --restart=always -e DUBBO_IP_TO_REGISTRY=192.168.140.85 -p 17778:17999 -p 20880:20880 -v /home/jar/autoApi2.0/File:/home/jar/autoApi2.0/File -v /home/jenkins/workspace/Api-Python-Script/stress-result/:/home/jenkins/workspace/Api-Python-Script/stress-result/ autoapi-test:${BUILD_VERSION}




imageId=$(docker ps -q -f name=^/autoapi-official\$)

# 判断容器是否存在

if [ -n "$imageId" ]; then

  docker stop autoapi-official
  
  docker rm autoapi-official
    
  num=${API_OFFICIAL_VERSION: 4: 4}
  new_num=$(expr $num - 1)
  newVersion=${API_OFFICIAL_VERSION: 0: 4}$new_num
  
  docker rmi autoapi-official:${newVersion}

fi

cd /home/jenkins/workspace/Auto-Api-official

# 复制jar包到Dockerfile所在的目录
cp /home/jenkins/workspace/Auto-Api-official/target/API_project-0.0.1-SNAPSHOT.jar /home/jenkins/workspace/Auto-Api-official


# 创建镜像
docker build -t autoapi-official:${API_OFFICIAL_VERSION} .


# 启动容器
docker run -d --name=autoapi-official --restart=always -p 17118:17999 -e DUBBO_IP_TO_REGISTRY=192.168.140.85 -p 20881:20881 -v /home/jar/autoApi2.0/File:/home/jar/autoApi2.0/File -v /home/jenkins/workspace/Api-Python-Script/stress-result/:/home/jenkins/workspace/Api-Python-Script/stress-result/ autoapi-official:${API_OFFICIAL_VERSION}


# 推送到阿里云
docker login --username=realsunmengqiao -p smq981230 registry.cn-hangzhou.aliyuncs.com

docker tag autoapi-official:${API_OFFICIAL_VERSION} registry.cn-hangzhou.aliyuncs.com/etlgroup/autoapi:${API_OFFICIAL_VERSION}

docker push registry.cn-hangzhou.aliyuncs.com/etlgroup/autoapi:${API_OFFICIAL_VERSION}

```



## Java Web项目规范

1.  总体上分为三大层 

- - `项目根目录/src/main/java`：放置项目Java源代码

- - `项目根目录/src/main/resources`：放置项目静态资源和配置文件

- - `项目根目录/src/test/java`：放置项目测试用例代码

1.  java 源代码的组织结构  

```plain
|_annotation：放置项目自定义注解
|_aspect：放置切面代码
|_config：放置配置类
|_constant：放置常量、枚举等定义
   |__consist：存放常量定义
   |__enums：存放枚举定义
|_controller：放置控制器代码
|_filter：放置一些过滤、拦截相关的代码
|_mapper：放置数据访问层代码接口
|_model：放置数据模型代码
   |__entity：放置数据库实体对象定义
   |__dto：存放数据传输对象定义
   |__vo：存放显示层对象定义
|_service：放置具体的业务逻辑代码（接口和实现分离）
   |__intf：存放业务逻辑接口定义
   |__impl：存放业务逻辑实际实现
|_utils：放置工具类和辅助代码
```

1.  resource 资源目录下的结构  

```plain
|_mapper：存放mybatis的XML映射文件（如果是mybatis项目）
|_static：存放网页静态资源，比如下面的js/css/img
   |__js：
   |__css：
   |__img：
   |__font：
   |__等等
|_template：存放网页模板，比如thymeleaf/freemarker模板等
   |__header
   |__sidebar
   |__bottom
   |__XXX.html等等
|_application.yml       基本配置文件
|_application-dev.yml   开发环境配置文件
|_application-test.yml  测试环境配置文件
|_application-prod.yml  生产环境配置文件
```



参考：[看完这篇，别人的开源项目结构应该能看懂了 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/115403195)

1.  命名规范  

| 类型   | 约束                                                         | 例                                             |
| ------ | ------------------------------------------------------------ | ---------------------------------------------- |
| 项目名 | 全部小写，多个单词用中划线分隔‘-’                            | spring-cloud                                   |
| 包名   | 全部小写                                                     | com.alibaba.fastjson                           |
| 类名   | 单词首字母大写                                               | Feature, ParserConfig,DefaultFieldDeserializer |
| 变量名 | 首字母小写，多个单词组成时，除首个单词，其他单词首字母都要大写 | password, userName                             |
| 常量名 | 全部大写，多个单词，用'_'分隔                                | CACHE_EXPIRED_TIME                             |
| 方法   | 同变量                                                       | read(), readObject(), getById()                |

1.  类命名  

| 属性           | 约束                                      | 例                                                           |
| -------------- | ----------------------------------------- | ------------------------------------------------------------ |
| 抽象类         | Abstract 或者 Base 开头                   | BaseUserService                                              |
| 枚举类         | Enum 作为后缀                             | GenderEnum                                                   |
| 工具类         | Utils作为后缀                             | StringUtils                                                  |
| 异常类         | Exception结尾                             | RuntimeException                                             |
| 接口实现类     | 接口名+ Impl                              | UserServiceImpl                                              |
| 领域模型相关   | /DO/DTO/VO/DAO                            | 正例：UserDAO 反例： UserDo， UserDao                        |
| 设计模式相关类 | Builder，Factory等                        | 当使用到设计模式时，需要使用对应的设计模式作为后缀，如ThreadFactory |
| 处理特定功能的 | Handler，Predicate, Validator             | 表示处理器，校验器，断言，这些类工厂还有配套的方法名如handle，predicate，validate |
| 测试类         | Test结尾                                  | UserServiceTest，表示用来测试UserService类的                 |
| MVC分层        | Controller，Service，ServiceImpl，DAO后缀 | UserManageController，UserManageDAO                          |

1.  注释 

- - 类、类属性、类方法的注释必须使用 Javadoc 规范，使用/**内容*/格式，不得使用 // xxx 方式。

- - 方法内部单行注释，在被注释语句上方另起一行，使用//注释。方法内部多行注释 使用/ /注释，注意与代码对齐。

- - 所有的抽象方法（包括接口中的方法）必须要用 Javadoc 注释、除了返回值、参数、 异常说明外，还必须指出该方法做什么事情，实现什么功能。
    所有的类都必须添加创建者和创建日期。

- - 所有的枚举类型字段必须要有注释，说明每个数据项的用途。

1.  注释例 

1. 1.  类注释  

```java
/**
 * Copyright (C), 2019-2020, Jann  balabala...
 *
 * 类的介绍：这是一个用来做什么事情的类，有哪些功能，用到的技术.....
 *
 * @author   类创建者姓名 保持对齐
 * @date     创建日期 保持对齐
 * @version  版本号 保持对齐
 */
```

1. 1.  属性注释  

```java
/** 提示信息 */
private String userName;
/**
 * 密码
 */
private String password;
```

1. 1.  方法注释  

```java
/**
 * 方法的详细说明，能干嘛，怎么实现的，注意事项...
 *
 * @param xxx 	 参数1的使用说明， 能否为null
 * @return 返回结果的说明， 不同情况下会返回怎样的结果
 * @throws 异常类型   注明从此类方法中抛出异常的说明
 */
```

1. 1.  构造方法注释  

```java
/**
 * 构造方法的详细说明
 *
 * @param xxx 	 参数1的使用说明， 能否为null
 * @throws 异常类型   注明从此类方法中抛出异常的说明
 */
```



参考：[告别编码5分钟，命名2小时！史上最全的Java命名规范参考！ - Java填坑笔记 - 博客园 (cnblogs.com)](https://www.cnblogs.com/liqiangchn/p/12000361.html#2118622294)



《阿里巴巴Java开发手册》

## Swagger3注解

Swagger3与2注解对应关系为：

```java
// 左为Swagger2，右为Swagger3
@ApiParam -> @Parameter
@ApiOperation -> @Operation
@Api -> @Tag
@ApiImplicitParams -> @Parameters
@ApiImplicitParam -> @Parameter
@ApiIgnore -> @Parameter(hidden = true) or @Operation(hidden = true) or @Hidden
@ApiModel -> @Schema
@ApiModelProperty -> @Schem
```

### 类

@Api：用在请求的类上，表示对类的说明

​    tags="说明该类的作用，可以在UI界面上看到的注解"

​    value="该参数没什么意义，在UI界面上也看到，所以不需要配置"

### 方法上

@ApiOperation：用在请求的方法上，说明方法的用途、作用

​    value="说明方法的用途、作用"

​    notes="方法的备注说明"

​    response：返回的对象

​		tags：这个方法会被单独摘出来，重新分组.若没有，所有的方法会在一个Controller分组下

### 方法入参

@ApiParam()：对方法参数的具体说明，用在方法入参括号里，该注解在post请求参数时，参数名不显示，这方面不如@ApiImplicitParam。

​		name：参数名

​		value：参数说明

​		required：是否为必须参数



@ApiImplicitParams：用在请求的方法上，表示一组参数说明

​    @ApiImplicitParam：用在@ApiImplicitParams注解中，指定一个请求参数的各个方面

​        name：参数名

​        value：参数的汉字说明、解释

​        required：参数是否必须传

​        paramType：参数放在哪个地方

​            · header --> 请求参数的获取：@RequestHeader

​            · query --> 请求参数的获取：@RequestParam

​            · path（用于restful接口）--> 请求参数的获取：@PathVariable

​            · div（不常用）

​            · form（不常用）    

​        dataType：参数类型，默认String，其它值dataType="Integer"       

​        defaultValue：参数的默认值



@ApiResponses：用在请求的方法上，表示一组响应

​    @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息

​        code：数字，例如400

​        message：信息，例如"请求参数没填好"

​        response：抛出异常的类

### 实体

@ApiModel：用于Model响应类上，表示一个返回响应数据的信息

​            （这种一般用在post创建的时候，使用@RequestBody这样的场景，

​            请求参数无法使用@ApiImplicitParam注解进行描述的时候）

​    value： model的别名，默认为类名，很少使用

​		description： model的详细描述，不要出现相同，否则会将相同vo内字段合并

​    @ApiModelProperty：用在属性上，描述Model响应类的属性

​    		value： 属性简短描述

​		example： 属性的示例值

​		required： 是否为必须值

### file入参

需要使用@RequestPart注解，例如：

```java
@ApiOperation(value = "上传文件接口",notes = "上传文件接口")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "name", value = "上传人")
    })
    @PostMapping(value = "/uploadFile")
    public String uploadFile(@RequestParam("name") String name,@RequestPart("file") MultipartFile file)
    }
```

### header参数

```java
@ApiImplicitParams({      @ApiImplicitParam(paramType="header",name="USERTOKEN",dataType="String",required=true,value="用户token")
    })
```

\-----------------------------------

参考：

https://www.5axxw.com/wiki/content/1u9xu6

https://blog.51cto.com/u_15127658/4098108

http://t.zoukankan.com/geekdc-p-13879913.html

swagger3新特性详见：https://www.codenong.com/cs105347336/

### 用例

```java
package com.itdragon.server.api.rest

import io.swagger.annotations.*
import org.springframework.format.annotation.DateTimeFormat
import org.springframework.http.ResponseEntity
import org.springframework.web.bind.annotation.*
import springfox.documentation.annotations.ApiIgnore
import java.util.*
import javax.servlet.http.HttpServletResponse

@Api(tags = ["SwaggerDemo"])
@RestController
@RequestMapping("/itdragon")
class ITDragonSwagger2Controller {


    @ApiOperation("方法说明", notes = "通过ApiOperation注解修饰方法，对方法做详细介绍。若不使用，Swagger会以函数名作为描述。", httpMethod = "GET")
    @GetMapping("/ApiOperation/info")
    fun getITDragonApiOperationInfo(@RequestParam("ids") ids: String): ResponseEntity<Unit> {
        return ResponseEntity.ok(Unit)
    }

    @ApiOperation("参数说明", notes = "通过ApiImplicitParams注解修饰参数，对参数做详细介绍。若不适用，")
    @ApiImplicitParams(value = [
        ApiImplicitParam(name = "deviceIds", value = "设备ID集合，用逗号区分", required = true, dataType = "String", paramType = "query"),
        ApiImplicitParam(name = "search", value = "查询字段")])
    @GetMapping("/ApiImplicitParams/info")
    fun getITDragonApiImplicitParamsInfo(@RequestParam("deviceIds") deviceIds: String,
                                         @RequestParam("search") search: String,
                                         @ApiIgnore currentUser: String): ResponseEntity<Unit> {
        return ResponseEntity.ok(Unit)
    }

    @ApiOperation("对象参数说明", notes = "")
    @PostMapping("/ApiModel/info")
    fun postITDragonApiModelInfo(@RequestBody itDragonCreateInfo: ITDragonCreateInfo) {
    }

    @GetMapping("/Native/info")
    fun getITDragonNativeInfo(@RequestParam("active", required = false) active: Boolean?,
                              @RequestParam("search", required = false) search: String?,
                              @RequestParam("startTime", required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") startTime: Date?,
                              @RequestParam("endTime", required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") endTime: Date?,
                              @RequestParam("page", required = false, defaultValue = "1") page: Int = 1,
                              @RequestParam("size", required = false, defaultValue = "10") size: Int = 10): ResponseEntity<Unit> {
        return ResponseEntity.ok(Unit)
    }

}

@ApiModel("创建用户模型")
class ITDragonCreateInfo {
    @ApiModelProperty("用户账号,登录账号")
    var username: String? = null
    var nikename: String? = null
}
```

## 问题

### 问题1

单体应用，spring-boot-starter-web与spring-cloud-starter-gateway不相容且exclusion排除无效，除非exclusion *但这样就无法使用springcloud。maven tree找不到包冲突。

解决方案：观察IDEA新建项目的pom文件解决方案，改为使用spring-boot-starter-webflux和spring-boot-starter-tomcat，排除spring-boot-starter-tomcat，yml中spring.main.web-application-type=reactive

### 问题2

本地可以起服务、输出正确结果，但部署到服务器不行
最终发现是服务器未配置时区，以及neo4j与oracle驱动版本不匹配

### 问题3

测试服服务可用，正式服不可用，重启无效

原因：有应用一直在tcp请求应用的接口文档，且该请求被阻塞，导致即使重启正式服端口也一直被占用

## 小问题

### 在线测试后端跨域请求

尝试不同的跨域配置时出错。

需要在部署到正式服前测试后端跨域。

chrome浏览器打开任意一个无关网页，F12调出开发者工具。

在Console输入下面的代码：



```jsx
var xhr = new XMLHttpRequest();
xhr.open('GET', 'https://www.xxx.com/api/action');
xhr.send(null);
xhr.onload = function(e) {
    var xhr = e.target;
    console.log(xhr.responseText);
}
```

回车运行代码。



### 数据库插入耗时太久

原先只需要查询，现在发现加入的数据库日志插入操作耗时很不稳定且一定数倍于简单查询，故最终不持久化日志，只记录到日志文件中，将数据持久化

最后，将日志记录放入子线程，不延长接口返回时间。对正在工作的表，尽量不添加新字段，而是建新表。



### SimpleDateFormat线程不安全

可以改为 DateTimeFormatter，或用ThreadLocalMap对象以提高format对象利用率。

```java

public class SimpleDateFormatTest {
    
    static ThreadLocal<SimpleDateFormat> local= new ThreadLocal<SimpleDateFormat>(){
        @Override
        protected SimpleDateFormat initialValue(){
            return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        }
    };
 
    public static void main(String[] args) {
        
        for (int i = 0; i < 5; ++i) {
            Thread thread = new Thread(() -> {
                public void run() {
                    try {
                        System.out.println(local.get().parse("2022-01-26 13:23:16"));
                    } catch (ParseException e) {
                        e.printStackTrace();
                    }finally {
                        //TODO 线程运行结束清除，避免内存泄露
                        local.remove();
                    }
                }
            });
 
            thread.start();
        }
    }
 
}
```

