---
layout:   post
title:    springboot与缓存
subtitle: Hello World
date:     2019-07-25
author:   BY 人间喜剧
header-img: img/post-bg-swift2
catalog: ture
tags:
     - springboot
---
# 一、JSR107缓存规范
#### Java Caching定义了2个核心接口，分别是CachingProvider，CacheManager，Cache，Entry和Expriy。
- CachingProvider：定义了创建，配置，获取，管理和控制多个CachManager，一个应用可以在运行期间访问多个CacehingProvider。
- CacheManager定义了创建，配置，获取，管理和控制多个唯一命名的Cache，这些Cache存在于CacheManager的上下文中，一个CacheManager仅仅被一个CachingProvider所拥有。
- Cache是一个类似Map的数据结构并且临时存储以Key为索引的值。一个Cache仅仅被一个CacheManager所拥有。
- Entry是个存储Cache中的key-value对。
- Expiry每一个存储在Cache中的条目有一个定义的有效期，一旦超过这个时间，条目为过期的状态。一旦国企，条目不能再次访问，更新和删除缓存的有效期可以通过ExpriyPolicy设置。
![](http://m.qpic.cn/psc?/V122tq584EfxP9/z4wcfyXMQjQ.Ifdxv6x54khtaZmP55LimMErg2cHADtRdFOj9ukXjXQiWUFKqpv1A11rQBZDCBwB3aHZRfgGxA!!/b&bo=PAS7AgAAAAADB6M!&rf=viewer_4)
如果我们需要使用JSR107我们就要导入javax.Cache包
# 二、Spring缓存抽象
Spring从3.1开始定义了org.springframework.cache.Cache
和org.springframework.cache.CacheManager接口来统一不同的缓存技术；
并支持使用JCache（JSR-107）注解简化我们开发；

- Cache接口为缓存的组件规范定义，包含缓存的各种操作集合
- Cache接口下Spring提供了各种xxxCache的实现；如RedisCache，EhCacheCache , ConcurrentMapCache等；
- 每次调用需要缓存功能的方法时，Spring会检查检查指定参数的指定的目标方法是否已经被调用过；如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法并缓存结果后返回给用户。下次调用直接从缓存中获取。
- 使用Spring缓存抽象时我们需要关注以下两点；
    1. 确定方法需要被缓存以及他们的缓存策略
    2. 从缓存中读取之前缓存存储的数据
![](http://m.qpic.cn/psc?/V122tq584EfxP9/z4wcfyXMQjQ.Ifdxv6x54vZ8CF9EKCICl7Ngw1b28.3*aoO4znbWsR63glnzAbvUbYvAPy8UVRfevPmaXqjobA!!/b&bo=lgRdAgAAAAADB.8!&rf=viewer_4)
### 环境搭建：
在这里 我们的maven引入的是
```
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```
    这个包，然后进行整合我们mybatis。
##### 注意看到们我们配置文件的格式

```
spring.datasource.url=jdbc:mysql://39.97.219.116:3306/mybatis
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```
在这里因为配置文件的格式写的不正确，调错了好长时间。
### 搭建基本环境：
1. 创建数据库的表：department与employee表。
2. 创建javabean封装数据
3. 整合mybatis操作数据库
    1. 配置数据源信息
    2. 使用注解版mybatis
        1. @MapperScan指定需要扫描的mapper接口所在的包
### 快速体验缓存
#### 步骤
1. 开启基于注解的缓存
2. 标注缓存的注解即可
    * @Cacheable
    * CacheEvict
    * CachePut
#### 讲解
在我们之前的mybatis的环境整合的时候，我们加入了一个查询方法，在我们开始查询之前我们并没有用到缓存。在我们每一次的查询，都会向数据库发送求情。都会在数据库中进行查询。
<br>我们开启我们日志就可以看到效果
开启数据库查询日志
```
logging.level.cn.yang.demo.mapper=debug
```
![](http://m.qpic.cn/psc?/V122tq584EfxP9/z4wcfyXMQjQ.Ifdxv6x54mOVtC0iNpyhY42LSDN2aNK.Hf0JtaYaBgMBpPYeR6tEtC00Hhc746CDWDc.C7.gnw!!/b&bo=Cgf.AAAAAAADB9E!&rf=viewer_4)

但是当我们在
```
@Cacheable
    public Employee getEmp(Integer id) {
        System.out.println("查询"+id+"号员工");
        Employee employee = employeeMapper.queryEmpById(id);
        Logger logger= LoggerFactory.getLogger(EmployeeService.class);
        logger.info(String.valueOf(employee));
       return employee;
    }
```
服务层上加上Cacheable注解后，这就开启我们的缓存了。这个注解作用就是，将我们方法的运行结果进行缓存，以后如果再要相同的结构，直接可以从缓存中获取，而不用调用方法了
#### @Cacheable的几个属性
在之前的CacheManager管理多个Cache组件的，对缓存真正的CRUD操作哦在cache组件中，每一个缓存组件都有自己唯一的名字
1. cahceNames/value:缓存的名字；
![](http://m.qpic.cn/psc?/V122tq584EfxP9/z4wcfyXMQjQ.Ifdxv6x54rudvs9dtHU5ACjVukTNWkL*e2yPE7cgAryZk2Lbyx.eX4LgcwS93kMC.is05CmHFQ!!/b&bo=AgQtAgAAAAADBws!&rf=viewer_4)
2. key：缓存数据使用的是key；可以用它来指定。默认是方法的参数值，1-方法的返回值。
```
@Cacheable(cacheNames = "emp",key ="#id")
    public Employee getEmp(Integer id) {
        System.out.println("查询"+id+"号员工");
        Employee employee = employeeMapper.queryEmpById(id);
        Logger logger= LoggerFactory.getLogger(EmployeeService.class);
        logger.info(String.valueOf(employee));
        return employee;
    }
```
在这里#id的值就是我们传入参数的值
![](http://a1.qpic.cn/psc?/V122tq584EfxP9/z4wcfyXMQjQ.Ifdxv6x54hOlYMyr2hprFQB632dTECbg7wFvWG.2*FXsqPlwGgWX*YB625PjIsWyhD.iyWcwIg!!/b&ek=1&kp=1&pt=0&bo=NwVAAgAAAAADF0I!&tl=1&vuin=1337734586&tm=1592722800&sce=60-2-2&rf=viewer_4)这个图就是我们key可以传达的spel表达式。
3. keyGenerator：key的生成器可以自己指定key的生成器的组件.key/keyGenerator:而选一。
4. CacheManager：指定缓存管理器：或者CacheResolver指定获取解析器
5. condition：指定符合条件的情况下才缓存。当表达是true的时候，才缓存。
6. unless：否定缓存，当我们表达式的值是fales的时候，才缓存。
7. sync：缓存是否使用异步模式
### 配置原理：
1. 自动配置类：
    * CacheAutoConfiguration
        * selectImports()方法上打断点，debug运行
        ![](http://m.qpic.cn/psc?/V122tq584EfxP9/z4wcfyXMQjQ.Ifdxv6x54sdnDL1De2S.boBjtfjfEllgxWIbnL7HtXlmEgDFJmEBnheqasZ*vcL3KcyP1Pa5BA!!/b&bo=vwRMAQAAAAADB9Q!&rf=viewer_4),显示出缓存的配置类。
2. 缓存的配置类：
    *   org.springframework.boot.autoconfigure.cache.GenericCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.JCacheCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.EhCacheCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.HazelcastCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.InfinispanCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.CouchbaseCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.RedisCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.CaffeineCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.GuavaCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.SimpleCacheConfiguration【默认】
     *   org.springframework.boot.autoconfigure.cache.NoOpCacheConfiguration
3. 在配置文件中，先会判断条件，根据判断的条件会开启相应的配置类，接下来我们看一下哪个配置类会生效：
    1. SimpleCacheConfiguration:
        * 这个类给我们的容器中注册了一个ConcurrentCacheManager。这个缓存管理器的作用是根据缓存的名字来获取缓存组件。所有的缓存的名字都会放到一个map中。在这个类中的getCache（）方法中，会先判断是否有这个名字的缓存，如果没有，则创建（使用createConcurrentMapCache方法）。
        * 在上边createConcurrentMapCache这个方法中，我们就会创建一个叫做ConcurrentMapCache的缓存组件。
        * 然后我们ConcurrentMapCache的作用就是将缓存数据保存在ConcurrentMap中；
    2. 运行流程
        * @Cacheable：
        1. 方法运行之前，先去查询Cache（缓存组件），按照cacheNames指定的名字获取；
           （CacheManager先获取相应的缓存），第一次获取缓存如果没有Cache组件会自动创建。
          2. 去Cache中查找缓存的内容，使用一个key，默认就是方法的参数；
             key是按照某种策略生成的；默认是使用keyGenerator生成的，默认使用SimpleKeyGenerator生成key； SimpleKeyGenerator生成key的默认策略；
                * 如果没有参数；key=new SimpleKey()；
                * 如果有一个参数：key=参数的值
                * 如果有多个参数：key=new SimpleKey(params)；
           3. 没有查到缓存就调用目标方法；
           4. 将目标方法返回的结果，放进缓存中
        #### @Cacheable标注的方法执行之前先来检查缓存中有没有这个数据，默认按照参数的值作为key去查询缓存，如果没有就运行方法并将结果放入缓存；以后再来调用就可以直接使用缓存中的数据；
    3. 核心：
        1. 使用CacheManager【ConcurrentMapCacheManager】按照名字得到Cache【ConcurrentMapCache】组件
        2. key使用keyGenerator生成的，默认是SimpleKeyGenerator
 *   几个属性：
     * cacheNames/value：指定缓存组件的名字;将方法的返回结果放在哪个缓存中，是数组的方式，可以指定多个缓存；
     *  key：缓存数据使用的key；可以用它来指定。默认是使用方法参数的值  1-方法的返回值
     *  编写SpEL； #i d;参数id的值   #a0  #p0  #root.args[0]
     * getEmp[2]
     *
     *  keyGenerator：key的生成器；可以自己指定key的生成器的组件id
     * key/keyGenerator：二选一使用;
     *
     *
     * cacheManager：指定缓存管理器；或者cacheResolver指定获取解析器
     * condition：指定符合条件的情况下才缓存；,condition = "#id>0"
     * condition = "#a0>1"：第一个参数的值》1的时候才进行缓存
     *
     *      unless:否定缓存；当unless指定的条件为true，方法的返回值就不会被缓存；可以获取到结果进行判断
     *              unless = "#result == null"
     *              unless = "#a0==2":如果第一个参数的值是2，结果不缓存；
     * sync：是否使用异步模式
#### CachePut:即调用方法（连接数据库的方法），又更新缓存。
修改了数据库的某个数据，然后又进行了缓存；
* 运行时机：
    1. 先调用目标方法
    2. 将目标方法的结果缓存起来。
* 测试步骤：
    1. 查询一号员工；查到的结果或放到缓存中。   
        * key：1 value：lastname：张三
    2. 以后查询还是之前的结果
    3. 更新1号员工；【lastName:zhangsan；gender:0】	
    * 将方法的值也放进缓存了。
    * key：传入的employee对象  值：返回的employee对象；
    4. 查询1号员工？ 应该是更新后的员工；
    应该是更新后的员工；
     *  key = "#employee.id":使用传入的参数的员工id；
     *  key = "#result.id"：使用返回后的id
     *             @Cacheable的key是不能用#result
     *  为什么是没更新前的？【1号员工没有在缓存中更新】
     ```
     @CachePut(/*value = "emp",*/key = "#result.id")
    public Employee updateEmp(Employee employee){
        System.out.println("updateEmp:"+employee);
        employeeMapper.updateEmp(employee);
        return employee;
    }

     ```
     所以其实是更新了，只是我们在缓存的时候，key不一样。所以我们应该把key放进去
###  @CacheEvict：缓存清除
1. key：指定要清除的数据
2.  allEntries = true：指定清除这个缓存中所有的数据
3.  beforeInvocation = false：缓存的清除是否在方法之前执行.默认代表缓存清除操作是在方法执行之后执行;如果出现异常缓存就不会清除
4.  beforeInvocation = true：代表清除缓存操作是在方法运行之前执行，无论方法是否出现异常，缓存都清除
### @Caching:定义复杂的缓存规则
```
 // @Caching 定义复杂的缓存规则
    @Caching(
         cacheable = {
             @Cacheable(/*value="emp",*/key = "#lastName")
         },
         put = {
             @CachePut(/*value="emp",*/key = "#result.id"),
             @CachePut(/*value="emp",*/key = "#result.email")
         }
    )
    public Employee getEmpByLastName(String lastName){
        return employeeMapper.getEmpByLastName(lastName);
    }

```
可以使用@CacheConfig注解来注解方法可以公共的部分比如
```

@CacheConfig(cacheNames="emp"/*,cacheManager = "employeeCacheManager"*/) //抽取缓存的公共配置
```
#### 以上的学习知道了，在springboot使用缓存的时候，默认使用的是ConcurrentMapCacheManager这个组件，这个组件的作用是将数据保存在了ConcurrentMap中。但是在真实的开发中，我们一般会使用缓存中间件：redis，memcache，ehcache
# redis：
## redis环境与测试
首先我们需要在docker镜像中下载redis 
```
docker pull redis
```
然后我们查看docker运行的项目
```
docker images
```
然后根据redis启动redis
```
docker run -d -p 6379:6379 --name myredis 镜像名
```
最后我们docker ps查看镜像运行的状态。
## 接下来我们开始整合springboot与redis。
首先导入spring-boot-starter-data-redis的jar包。根据springboot使用方法，所有的配置都在AutoConfig包里，所以我们这里可以直接引用springboot自动配置类的模板

找到RedisAutoConfiguration这个自动配置类，我们可以看到：
```

 @Bean
    @ConditionalOnMissingBean(
        name = {"redisTemplate"}
    )
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<Object, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
```
里边的两个方法
1. RedisTemplate类就好像我们JDBCTemplate。用来连接redis与程序的。它可以将redis的存取操作不同的对象。
2. StringRedisTemplate：那我们知道在redis中大部分的数据存储是
#### 使用redis模板对象对数据库进行交互。
```
redisTemplate.opsForList().leftPush("k2","hello world");
stringRedisTemplate.opsForList().leftPush("k3","nihao");
```
记住我们在用redisTemplate保存对象的时候，被保存的必须要被序列化。一般的序列化我们只需要实现Serializable接口就可以。但是在这里我们可以自己写一个可以实现自定义类的序列化方法。
```
@Configuration
public class MyRedisConfig {
    @Bean
    public RedisTemplate<Object, Employee> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<Object, Employee> template = new RedisTemplate();
        template.setConnectionFactory(
                redisConnectionFactory);
        Jackson2JsonRedisSerializer<Employee> ser=new Jackson2JsonRedisSerializer<Employee>(Employee.class);
        //这个方法需要传入一个
        template.setDefaultSerializer(ser);
        return template;
    }

}

```
在调用的时候我们可以直接调用
```
 @Autowired
    RedisTemplate<Object,Employee> redisTemplate;
    @Test
    public void getRedis(){
        redisTemplate.opsForList().leftPush("Emp",new Employee());
    }
```
#### 自定义配置CacheManager
我们之前说过在springboot中使用的默认CacheManager是SimpleCacheConfiguration，将缓存存在ConcurrentMap中，但是我们现在开始使用了redis缓存技术，当引入了redis的时候，我们的CacheManager开始变成了
![](http://a1.qpic.cn/psc?/V122tq584EfxP9/z4wcfyXMQjQ.Ifdxv6x54lYbHz3m*8eu4Xs6p1idYAK46RizlTqJM92fNS2hFJY20Ea7bCVqDiwoHtgyBmxL5w!!/b&ek=1&kp=1&pt=0&bo=AQfDAAAAAAADF*c!&tl=1&vuin=1337734586&tm=1595304000&sce=60-2-2&rf=viewer_4)
我们之前给Demo中的一个Employee类实现了一个序列化的配置，现在我们要自己写一个CacheManager对缓存进行配置
```
@Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        RedisCacheManager cm = RedisCacheManager.builder(connectionFactory).cacheDefaults(defaultCacheConfig()).withInitialCacheConfigurations(singletonMap("predefined", defaultCacheConfig().disableCachingNullValues()))
                .transactionAware()
                .build();;
        return RedisCacheManager.create(connectionFactory);
    }
```
这是按照开发文档自己写的，具体没有实现，如果真正有需求请参考
[RedisManager配置开发文档](https://docs.spring.io/spring-data/redis/docs/2.2.5.RELEASE/reference/html/#redis:support)
