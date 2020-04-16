---

title : halo✍源码阅读
toc : true 
categories: 源码学习
date: 2019-11-09 20:10:33
tags: [Java,Spring,Spring Boot,Spring Data JPA]
---

&#160; &#160; &#160; &#160;**Halo是一款现代化的个人独立博客系统，给习惯写博客的同学一个更好的选择。**据说这是一个较容易读懂的Spring-Boot项目，那我就希望通过这个项目学习前辈的经验。

&#160; &#160; &#160; &#160;如有帮助，不胜荣幸。如有错误，欢迎指正！

<!--more-->

# 前言

&#160; &#160; &#160; &#160;最早看到这个博客的源码的时候是通过B站up主-[**CodeSheep**](https://space.bilibili.com/384068749)的一个视频：[Java企业级开源项目推荐，帮助大家从学习走向实践](https://www.bilibili.com/video/av49644128)，奈何当时自己知识有限，没有仔细的阅读源码。近日Halo也推出了正式版，我也就抱着学习的心态拜读一下。

&#160; &#160; &#160; &#160;首先打开工程，看到整个工程有以下两个明显的变化：

- 配置文件由properties变为yaml

&#160; &#160; &#160; &#160;可以明显的看到，在处理层级关系的时候，properties需要使用大量的路径来描述层级（或者属性），比如environments.dev.url和environments.dev.name。其次，对于较为复杂的结构，比如数组（my.servers），写起来更为复杂。而对应的YAML格式文件就简单很多：

- 构建工具由Meavn变为Gradle

&#160; &#160; &#160; &#160;因为此前有过Android的开发经验，所以这一改变对我的影响并不大😄。Gradle逐渐替代meavn应该是目前的趋势，由于目前大部分教学和企业采用的还是以Meavn为主，所以此前我也未曾尝试过采用Gradle构建项目。由此看出Halo还是很Fashion的👍。

# Application

&#160; &#160; &#160; &#160;首先打开``Application.java``文件，看看有什么学习的地方🤤。

```java
@SpringBootApplication
@EnableJpaAuditing
@EnableScheduling
@EnableAsync
@EnableJpaRepositories(basePackages = "run.halo.app.repository", repositoryBaseClass = BaseRepositoryImpl.class)
public class Application extends SpringBootServletInitializer {

    public static void main(String[] args) {
        // Customize the spring config location
        System.setProperty("spring.config.additional-location", "file:${user.home}/.halo/,file:${user.home}/halo-dev/");

        // Run application
        SpringApplication.run(Application.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        System.setProperty("spring.config.additional-location", "file:${user.home}/.halo/,file:${user.home}/halo-dev/");
        return application.sources(Application.class);
    }
}
```

&#160; &#160; &#160; &#160;发现与我之前Spring-Boot开发两点不一样的地方：

&#160; &#160; &#160; &#160;**其一：继承SpringBootServletInitializer从而实现configure方法**

&#160; &#160; &#160; &#160;可能找答案的姿势不对，只零星找到下面几点描述：

&#160; &#160; &#160; &#160;1、启动类继承SpringBootServletInitializer类，重写 configure(SpringApplicationBuilder builder`)方法

&#160; &#160; &#160; &#160;2、因为想要用web容器启动项目

&#160; &#160; &#160; &#160;3、使用外部 servlet 容器

结论：***为了使用Undertow容器！***

&#160; &#160; &#160; &#160;原因：

1、排除内置的tomcat容器-build.gradle

```
exclude module: 'spring-boot-starter-tomcat'
```

2、添加Undertow依赖-build.gradle

```
implementation 'org.springframework.boot:spring-boot-starter-undertow'
```

3、配置服务器

```yaml
server:
  port: 8090
  use-forward-headers: true
  undertow:
    io-threads: 2
    worker-threads: 36
    buffer-size: 1024
    directBuffers: true
```

&#160; &#160; &#160; &#160;结合此前在别人基础上修改的网盘项目得到了相同的印证。

&#160; &#160; &#160; &#160;**其二：System.setProperty("spring.config.additional-location", "...");**

&#160; &#160; &#160; &#160;开发者给了以下注释：`Customize the spring config location`（定制spring配置文件的位置）

&#160; &#160; &#160; &#160;存在以下三个疑问：

&#160; &#160; &#160; &#160;1、System.setProperty有何用？

&#160; &#160; &#160; &#160;setProperty (String prop, String value); 1、 设置指定键对值的系统属性，其中prop：系统属性的名称，value：系统属性的值。注：这里的system，系统指的是 JRE (runtime)system，不是指 OS。 2、System.setProperty相当于一个静态变量，存在内存里面，可以在项目的任何一个地方，通过System.getProperty("变量")来获得

&#160; &#160; &#160; &#160;2、为何要设定这个变量？

&#160; &#160; &#160; &#160;加载外部配置文件。打包jar运行也不方便修改jar内部数据，通过设置环境变量spring.config.location。因为博客中也有很多配置选项，所以猜想需要从外部读取某些会改变的配置，需要继续阅读源码验证猜想。

&#160; &#160; &#160; &#160;3、${user.home}从何而来？

&#160; &#160; &#160; &#160;如从前所示user.home应该是一个静态变量，尝试打印：

```java
System.out.println(System.getProperty("user.home"));
//打印出：C:\Users\74472 验证猜想成功
```


| 变量 | 含义 |
| ---- | ---- |
| java.version | Java 运行时环境版本 |
| java.vendor | Java 运行时环境供应商|
|java.vendor.url|Java 供应商的 URL|
|java.home|Java 安装目录|
|java.vm.specification.version|Java 虚拟机规范版本|
|java.vm.specification.vendor|Java 虚拟机规范供应商|
|java.vm.specification.name|Java 虚拟机规范名称|
|java.vm.version|Java 虚拟机实现版本|
|java.vm.vendor|Java 虚拟机实现供应商|
|java.vm.name|Java 虚拟机实现名称|
|java.specification.version|Java 运行时环境规范版本|
|java.specification.vendor|Java 运行时环境规范供应商|
|java.specification.name|Java 运行时环境规范名称|
|java.class.version|Java 类格式版本号|
|java.class.path|Java 类路径|
|java.library.path|加载库时搜索的路径列表|
|java.io.tmpdir|默认的临时文件路径|
|java.compiler|要使用的 JIT 编译器的名称|
|java.ext.dirs|一个或多个扩展目录的路径|
|os.name|操作系统的名称|
|os.arch|操作系统的架构|
|os.version|操作系统的版本|
|file.separator|文件分隔符（在 UNIX 系统中是“/”）|
|path.separator|路径分隔符（在 UNIX 系统中是“:”）|
|line.separator|行分隔符（在 UNIX 系统中是“/n”）|
|user.name|用户的账户名称|
|user.home|用户的主目录|
|user.dir|用户的当前工作目录|

# 测试运行

&#160; &#160; &#160; &#160;Spring-Boot的项目运行起来还是比较简单的🤪。页面也很美观👍![Halo博客](https://i.loli.net/2019/09/05/UjxFAirJIQ5TfB7.png)


# Controller学习

&#160; &#160; &#160; &#160;Spring-boot的项目通常都是从Controller读起，发现一个共同的特点，就是代码都是类似这样一个结构：

```java
private final PostService postService;

private final OptionService optionService;

... .... // 其他的Service

private final ThemeService themeService;

public ContentIndexController(PostService postService,
                              OptionService optionService,
                              ThemeService themeService) {
    this.postService = postService;
    this.optionService = optionService;
    ... .... // 其他的Service
    this.themeService = themeService;
}

@GetMapping
public String index(Model model) {
    return this.index(model, 1, Sort.by(DESC, "topPriority").and(Sort.by(DESC, "createTime")));
}

 @GetMapping(value = "page/{page}")
public String index(Model model,
                    @PathVariable(value = "page") Integer page,
                    @SortDefault.SortDefaults({
                        @SortDefault(sort = "topPriority", direction = DESC),
                        @SortDefault(sort = "createTime", direction = DESC)
                    }) Sort sort) {
    ...//省略   
}
```

&#160; &#160; &#160; &#160;**学习收获一：使用构造器注入需要用到的Service**

```java
private final TagService tagService;

private final PostTagService postTagService;

private final PostService postService;

public TagController(TagService tagService,
                     PostTagService postTagService,
                     PostService postService) {
    this.tagService = tagService;
    this.postTagService = postTagService;
    this.postService = postService;
}
```

&#160; &#160; &#160; &#160;**学习收获二：使用多态处理请求**

&#160; &#160; &#160; &#160;真正起作用的是后者，前者只是为用户添加了参数后调用后者。

```java
@GetMapping
public String archives(Model model) {
    return this.archives(model, 1, Sort.by(DESC, "createTime"));
}
@GetMapping(value = "page/{page}")
public String archives(Model model,
                       @PathVariable(value = "page") Integer page,
                       @SortDefault(sort = "createTime", direction = DESC) Sort sort) {
    Pageable pageable = PageRequest.of(page - 1, optionService.getPostPageSize(), sort);

    Page<Post> postPage = postService.pageBy(PostStatus.PUBLISHED, pageable);
    Page<PostListVO> postListVos = postService.convertToListVo(postPage);
    int[] pageRainbow = PageUtil.rainbow(page, postListVos.getTotalPages(), 3);

    model.addAttribute("is_archives", true);
    model.addAttribute("pageRainbow", pageRainbow);
    model.addAttribute("posts", postListVos);

    return themeService.render("archives");
}
```


&#160; &#160; &#160; &#160;**学习收获三：RESTful的api设计**

```java
@GetMapping("{slugName}/page/{page}")
```

# Repository学习

&#160; &#160; &#160;&#160;总的来说使用Spring Data JPA完成对数据库的增删改查,仔细阅读发现还是略有不同。在工程中Dao层结构如下：

```java
public interface UserRepository extends BaseRepository<User, Integer> {

    @NonNull
    Optional<User> findByUsername(@NonNull String username);

    @NonNull
    Optional<User> findByEmail(@NonNull String email);
}
```
&#160; &#160; &#160;&#160;**学习收获一：自定义Repository接口**
&#160; &#160; &#160;&#160;具体的步骤就不展开讲可以参考：[Spring Data JPA: 为所有Repository添加自定义方法](https://www.tianmaying.com/tutorial/spring-jpa-custom-all)
&#160; &#160; &#160;&#160;**学习收获二：Query Creation**

&#160; &#160; &#160;&#160;基于函数名创建查询：

```java
public interface UserRepository extends Repository<User, Long> {
 
  List<User> findByEmailAddressAndLastname(String emailAddress, String lastname);
}
```

&#160; &#160; &#160;&#160;上例的函数将会翻译成查询语句：`select u from User u where u.emailAddress = ?1 and u.lastname = ?2`

&#160; &#160; &#160;&#160;下表列出了JPA支持的关键字和对应的转换语句：

| Keyword             | Sample                                                       | JPQL snippet                                                 |
| :------------------ | :-------------------------------- | :----------------------------------------------- |
| `And`               | `findByLastnameAndFirstname`                                 | `… where x.lastname = ?1 and x.firstname = ?2`               |
| `Or`                | `findByLastnameOrFirstname`                                  | `… where x.lastname = ?1 or x.firstname = ?2`                |
| `Is,Equals`         | `findByFirstnameIs`,`findByFirstnameEquals` | `… where x.firstname = ?1`                                   |
| `Between`           | `findByStartDateBetween`                                     | `… where x.startDate between ?1 and ?2`                      |
| `LessThan`          | `findByAgeLessThan`                                          | `… where x.age < ?1`                                         |
| `LessThanEqual`     | `findByAgeLessThanEqual`                                     | `… where x.age <= ?1`                                        |
| `GreaterThan`       | `findByAgeGreaterThan`                                       | `… where x.age > ?1`                                         |
| `GreaterThanEqual`  | `findByAgeGreaterThanEqual`                                  | `… where x.age >= ?1`                                        |
| `After`             | `findByStartDateAfter`                                       | `… where x.startDate > ?1`                                   |
| `Before`            | `findByStartDateBefore`                                      | `… where x.startDate < ?1`                                   |
| `IsNull`            | `findByAgeIsNull`                                            | `… where x.age is null`                                      |
| `IsNotNull,NotNull` | `findByAge(Is)NotNull`                                       | `… where x.age not null`                                     |
| `Like`              | `findByFirstnameLike`                                        | `… where x.firstname like ?1`                                |
| `NotLike`           | `findByFirstnameNotLike`                                     | `… where x.firstname not like ?1`                            |
| `StartingWith`      | `findByFirstnameStartingWith`                                | `… where x.firstname like ?1` (parameter bound with appended `%`) |
| `EndingWith`        | `findByFirstnameEndingWith`                                  | `… where x.firstname like ?1` (parameter bound with prepended `%`) |
| `Containing`        | `findByFirstnameContaining`                                  | `… where x.firstname like ?1` (parameter bound wrapped in `%`) |
| `OrderBy`           | `findByAgeOrderByLastnameDesc`                               | `… where x.age = ?1 order by x.lastname desc`                |
| `Not`               | `findByLastnameNot`                                          | `… where x.lastname <> ?1`                                   |
| `In`                | `findByAgeIn(Collection<Age> ages)`                          | `… where x.age in ?1`                                        |
| `NotIn`             | `findByAgeNotIn(Collection<Age> ages)`                       | `… where x.age not in ?1`                                    |
| `True`              | `findByActiveTrue()`                                         | `… where x.active = true`                                    |
| `False`             | `findByActiveFalse()`                                        | `… where x.active = false`                                   |
| `IgnoreCase`        | `findByFirstnameIgnoreCase`                                  | `… where UPPER(x.firstame) = UPPER(?1)`                      |

&#160; &#160; &#160;&#160; **学习收获三：@Query 自定义查询**

```java
@Query("select new run.halo.app.model.projection.CommentCountProjection(count(comment.id), comment.postId) " +
       "from JournalComment comment " +
       "where comment.postId in ?1 group by comment.postId")
@NonNull
@Override
List<CommentCountProjection> countByPostIds(@NonNull Iterable<Integer> postIds);

```
# Service学习

## interface接口

&#160; &#160; &#160;&#160; 打开文件，发现工程中的Service中的方法是比较少的。

```java
public interface LogService extends CrudService<Log, Long> {

    Page<LogDTO> pageLatest(int top);
}
```

&#160; &#160; &#160;&#160; 奥秘在于接口继承了自定义的`CrudService`，而在该接口中对所有可能用到的crud方法进行总结：

```java
public interface CrudService<DOMAIN, ID> {

    @NonNull
    List<DOMAIN> listAll();

    @NonNull
    List<DOMAIN> listAll(@NonNull Sort sort);

    @NonNull
    Page<DOMAIN> listAll(@NonNull Pageable pageable);

    @NonNull
    List<DOMAIN> listAllByIds(@Nullable Collection<ID> ids);

    @NonNull
    List<DOMAIN> listAllByIds(@Nullable Collection<ID> ids, @NonNull Sort sort);

    @NonNull
    Optional<DOMAIN> fetchById(@NonNull ID id);

    @NonNull
    DOMAIN getById(@NonNull ID id);

    @Nullable
    DOMAIN getByIdOfNullable(@NonNull ID id);

    boolean existsById(@NonNull ID id);

    void mustExistById(@NonNull ID id);

    long count();

    @NonNull
    @Transactional
    DOMAIN create(@NonNull DOMAIN domain);

    @NonNull
    @Transactional
    List<DOMAIN> createInBatch(@NonNull Collection<DOMAIN> domains);

    @NonNull
    @Transactional
    DOMAIN update(@NonNull DOMAIN domain);

    void flush();
    
    @NonNull
    @Transactional
    List<DOMAIN> updateInBatch(@NonNull Collection<DOMAIN> domains);

    @NonNull
    @Transactional
    DOMAIN removeById(@NonNull ID id);

    @Nullable
    @Transactional
    DOMAIN removeByIdOfNullable(@NonNull ID id);

    @Transactional
    void remove(@NonNull DOMAIN domain);

    @Transactional
    void removeInBatch(@NonNull Collection<ID> ids);

    @Transactional
    void removeAll(@NonNull Collection<DOMAIN> domains);

    @Transactional
    void removeAll();
}

```

&#160; &#160; &#160;&#160; 并且编写抽象模板类实现它。这里部分的通用性是很强的，我认为在大部分场景下都可以直接借鉴这两个文件！

## impl实现类
&#160; &#160; &#160;&#160; 正如之前的controller一样使用构造器的方法注入依赖,包括配置文件中的信息`@Value("${spring.datasource.driver-class-name}") String driverClassName`。到此还没有仔细的阅读每一个impl，先对整个工程做一个大致的了解。

# Model

```java
@Data
public class BackupDTO {

    private String fileName;

    private Date createTime;

    private String fileSize;

    private String fileType;

    private String type;
}
```

&#160; &#160; &#160;&#160; 这一块更多的是lombok的使用可以参考[Lombok介绍、使用方法和总结](https://juejin.im/post/5d916b405188255e84032a59)

