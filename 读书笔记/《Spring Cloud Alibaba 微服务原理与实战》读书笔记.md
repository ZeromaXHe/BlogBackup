# 第1章 微服务的发展史

## 1.1 从单体架构到分布式架构的演进

### 1.1.1 单体架构

通常来说，如果一个 war 包或者 jar 包里面包含一个应用的所有功能，则我们称这种架构为单体架构。

### 1.1.2 集群及垂直化

业务场景越多越复杂，意味着 war 包中的代码量会持续上升，并且各个业务代码之间的耦合度也会越来越高，后期的代码维护和版本发布涉及的测试和上线，也会很困难。

我们可以从两个方面进行优化：

1. 通过横向增加服务器，把单台机器变成多台机器的集群。
2. 按照业务的垂直领域进行拆分，减少业务的耦合度，以及降低单个 war 包带来的伸缩性困难问题。

针对数据库进行垂直分库，主要是考虑到 Tomcat 服务器能够承载的流量大了之后，如果流量都传导到数据库上，会给数据库造成比较大的压力。

### 1.1.3 SOA

SOA（Service-Oriented Architecture），面向服务的架构。核心目标是把一些通用的、会被多个上层服务调用的共享业务提取成独立的基础服务，这些被提取出来的共享服务相对来说比较独立，并且可以重用。所以在 SOA 中，服务是最核心的抽象手段，业务被划分为一些粗粒度的业务服务和业务流程。

在 SOA 中，会采用 ESB（企业服务总线）来作为系统和服务之间的通信桥梁，ESB 本身还提供服务地址的管理、不同系统之间的协议转化和数据格式的转化等。调用端不需要关心目标服务的位置，从而使得服务之间的交互是动态的，这样做的好处是实现了服务的调用者和服务的提供者之间的高度解耦。

SOA 主要解决的问题是：

- 信息孤岛
- 共享业务的重用

### 1.1.4 微服务架构

从名字上看，面向对象（SOA）和微服务本质上都是服务化思想的一种体现。如果 SOA 是面向服务开发思想的雏形，那么微服务就是针对可重用业务服务的更进一步优化，我们可以把 SOA 看成微服务的超集，也就是多个微服务可以组成一个 SOA 服务。

实施微服务的前提是软件交付链路及基础设施的成熟化。

由于 SOA 和微服务两者的关注点不一样，造成了这两者有非常大的区别：

- SOA 关注的是服务的重用性及解决信息孤岛问题。
- 微服务关注的是解耦，虽然解耦和可重用性从特定角度来看是一样的，但本质上是有区别的，解耦是降低业务之间的耦合度，而重用关注的是服务的复用。
- 微服务会更多地关注在 DevOps 的持续交付上，因为服务粒度细化之后使得开发运维变得更加重要，因此微服务与容器化技术的结合更加紧密。

## 1.2 微服务架构带来的挑战

### 1.2.1 微服务架构的优点

- **复杂度可控**：通过对共享业务服务更细粒度的拆分，一个服务只需要关注一个特定的业务领域，并通过定义良好的接口清晰表述服务边界。由于体积小、复杂度低，开发、维护会更加简单。
- **技术选型更灵活**：每个微服务都由不同的团队来维护，所以可以结合业务特性自由选择技术栈。
- **可扩展性更强**：可以根据每个微服务的性能要求和业务特点来对服务进行灵活扩展，比如通过增加单个服务的集群规模，提升部署了该服务的节点的硬件配置。
- **独立部署**：由于每个微服务都是一个独立运行的进程，所以可以实现独立部署。当某个微服务发生变更时不需要重新编译部署整个应用，并且单个微服务的代码量比较小，使得发布更加高效。
- **容错性**：在微服务架构中，如果某一个服务发生故障，我们可以使故障隔离在单个服务中。其他服务可以通过重试、降级等机制来实现应用层面的容错。

### 1.2.2 微服务架构面临的挑战

- **故障排除**：一次请求可能会经历多个不同的微服务的多次交互，交互的链路可能会比较长，每个微服务会产生自己的日志，在这种情况下如果出现一个故障，开发人员定位问题的根源会比较困难。
- **服务监控**：在一个单体架构中很容易实现服务的监控，因为所有的功能都在一个服务中。在微服务架构中，服务监控开销会非常大，可以想象一下，在几百个微服务组成的架构中，我们不仅要对整个链路进行监控，还需要对每个微服务都实现一套类似单体架构的监控。
- **分布式架构的复杂性**：微服务本身构建的是一个分布式系统，分布式系统涉及服务之间的远程通信，而网络通信中网络的延迟和网络故障是无法避免的，从而增加了应用程序的复杂度。
- **服务依赖**：微服务数量增加之后，各个服务之间会存在更多的依赖关系，使得系统整体更为复杂。
- **运维成本**：在微服务中，需要保证几百个微服务的正常运行，对于运维的挑战是巨大的。比如单个服务流量激增时如何快速扩容、服务拆分之后导致故障点增多如何处理、如何快速部署和统一管理众多的服务等。

## 1.3 如何实现微服务架构

### 1.3.2 微服务架构下的技术挑战

- 分布式配置中心
- 服务路由
- 负载均衡
- 熔断限流
- 链路监控

# 第2章 微服务解决方案之 Spring Cloud

## 2.1 什么是 Spring Cloud

简单地说，Spring Cloud 提供了一些可以让开发者快速构建微服务应用的工具，比如配置管理、服务发现、熔断、智能路由等，这些服务可以在任何分布式环境下很好地工作。Spring Cloud 主要致力于解决如下问题：

- 分布式及版本化配置。
- 服务注册与发现
- 服务路由
- 服务调用
- 负载均衡
- 断路器
- 全局锁
- Leader 选举及集群状态
- 分布式消息

需要注意的是，Spring Cloud 并不是 Spring 团队全新研发的框架，它只是把一些比较优秀的解决微服务架构中常见问题的开源框架基于 Spring Cloud 规范进行了整合，通过 Spring Boot 这个框架进行再次封装后屏蔽掉了复杂的配置，给开发者提供良好的开箱即用的微服务开发体验。

不难看出，Spring Cloud 其实就是一套规范，而 Spring Cloud Netflix、Spring Cloud Consul、Spring Cloud Alibaba 才是 Spring Cloud 规范的实现。

## 2.2 Spring Cloud 版本简介

采用了伦敦地铁站的名字根据字母表的顺序结合对应版本的时间顺序来定义大版本。

Spring Cloud 的每个大版本通过 BOM （Bill Of Materials）来管理每个子项目的版本清单。

Spring Cloud 项目的发布内容积累到一个临界点或者解决一些严重的Bug后，会发布一个 Service Release 的版本，简称为 SR + 一个递增的数字

值得注意的是，Spring Cloud 中所有子项目都依赖 Spring Boot 框架，所以 Spring Boot 框架的版本号和 Spring Cloud 的版本号之间也存在依赖及兼容的关系。

## 2.3 Spring Cloud 规范下的实现

在 Spring Cloud 这个规范下，有很多实现：

- Spring-Cloud-Bus
- Spring-Cloud-Netflix
- Spring-Cloud-Zookeeper
- Spring-Cloud-Gateway

在这些实现中，绝大部分组件都使用“别人已经造好的轮子”，然后基于 Spring Cloud 规范进行整合，使用者只需要使用非常简单的配置即可完成微服务架构下复杂的需求。

这也是 Spring 团队最厉害的地方，它们很少重复造轮子。回想一下，最早的 Spring Framework，它只提供了 IoC 和 AOP 两个核心功能，对于 ORM、MVC 等其他的功能，Spring 都提供非常好的兼容性，比如 Hibernate、MyBatis、Struts 2。

只有在别人提供的东西不够好的情况下，Spring 团队才会考虑自己研发。比如 Struts 2 经常有安全漏洞，所以 Spring 团队自己研发了 Spring MVC 框架，并且成了现在非常主流的 MVC 框架。再比如 Spring Cloud Netflix 中的 Zuul 网关，因为性能及版本迭代较慢，所以 Spring 团队孵化了一个 Spring Cloud Gateway 来取代 Zuul。

Spring Cloud 生态下服务治理的解决方案主要有两个： Spring Cloud Netflix 和 Spring Cloud Alibaba。

## 2.4 Spring Cloud Netflix

包括以下组件：

- Eureka，服务注册与发现
- Zuul，服务网关
- Ribbon，负载均衡
- Feign，远程服务的客户端代理
- Hystrix，断路器，提供服务熔断和限流功能
- Hystrix Dashboard，监控面板
- Turbine，将各个服务实例上的 Hystrix 监控信息进行统一聚合

Spring Cloud Netflix 是 Spring Boot 和 Netflix OSS 在 Spring Cloud 规范下的集成。其中，Netflix OSS（Netflix Open Source Software）是由 Netflix 公司开发的一套开源框架和组件库，Eureka、Zuul 等都是 Netflix OSS 中的开源组件。

Netflix OSS 本身是一套非常好的组件，由于 Netflix 对 Zuul 1、Ribbon、Archaius 等组件的维护不利，Spring Cloud 决定在 Greenwich 中将如下项目都改为“维护模式”：

- Spring-Cloud-Netflix-Hystrix
- Spring-Cloud-Netflix-Ribbon
- Spring-Cloud-Netflix-Zuul

| 当前                        | 替换                                              |
| --------------------------- | ------------------------------------------------- |
| Hystrix                     | Resilience4j                                      |
| Hystrix Dashboard / Turbine | Micrometer + Monitoring System                    |
| Ribbon                      | Spring Cloud Loadbalancer                         |
| Zuul 1                      | Spring Cloud Gateway                              |
| Archaius 1                  | Spring Boot external config + Spring Cloud Config |

## 2.5 Spring Cloud Alibaba

以下是 Spring Cloud Alibaba 生态下的主要功能组件，这些组件包含开源组件和阿里云产品组件，云产品是需要付费使用的：

- Sentinel，流量控制和服务降级
- Nacos，服务注册与发现
- Nacos，分布式配置中心
- RocketMQ，消息驱动
- Seate，分布式事务
- Dubbo，RPC 通信
- OSS，阿里云对象存储（收费的云服务）

### 2.5.1 Spring Cloud Alibaba 的优势

- Alibaba 的开源组件在没有织入 Spring Cloud 生态之前，已经在各大公司广泛应用，所以集成到 Spring Cloud 生态使得开发者能够很轻松地实现技术整合及迁移。
- Alibaba 的开源组件在服务治理上和处理高并发的能力上有天然的优势，毕竟这些组件都经历过数次双11的考验，也在各大互联网公司大规模应用过。所以，相比 Spring Cloud Netflix 来说，Spring Cloud Alibaba 在服务治理这块的能力更适合国内的技术场景，同时，Spring Cloud Alibaba 在功能上不仅完全覆盖了 Spring Cloud Netflix 原生特性，而且还提供了更加稳定和成熟的实现。

# 第3章 Spring Cloud 的核心之 Spring Boot

Spring Boot 是帮助开发者快速构建一个基于 Spring Framework 及 Spring 生态体系的应用解决方案，也是 Spring Framework 对于“约定优于配置（Convention over Configuration）”理念的最佳实践。

## 3.1 重新认识 Spring Boot

Spring 是一个轻量级框架，它的主要目的是简化 JavaEE 的企业级应用开发，而达到这个目的的两个关键手段是 IoC/DI 和 AOP。除此之外，Spring 就像一个万能胶，对 Java 开发中的常用技术进行合理的封装和设计，使之能够快速方便地集成和开发，比如 Spring 集成 MyBatis、Spring 集成 Struts 等。

### 3.1.1 Spring IoC / DI

IoC（Inversion of Control）和 DI（Dependency Injection）的全称分别是控制反转和依赖注入。

IoC（控制反转）实际上就是把对象的生命周期托管到 Spring 容器中，而反转是指对象的获取方式被反转了。传统意义上的对象的创建是通过 new 来构建，这种方式会使代码之间的耦合度非常高。当使用 Spring IoC 容器之后，是直接从 IoC 容器中获得。

Spring IoC 容器中的对象的构建时机，在早期的Spring中，主要通过 XML 的方式来定义 Bean，Spring 会解析 XML 文件，把定义的 Bean 装载到 IoC 容器中。

DI（Dependency Inject），也就是依赖注入，简单理解就是 IoC 容器在运行期间，动态地把某种依赖关系注入组件中。

实现依赖注入的方法有三种，分别是接口注入、构造方法注入和 setter 方法注入。不过现在基本都基于注解的方式来描述 Bean 之间的依赖关系，比如 @Autowired、@Inject 和 @Resource。但是不管形式怎么变化，本质上还是一样的。

### 3.1.2 Bean 装配方式的升级

基于 XML 配置的方式很好地完成了对象声明周期的描述和管理，但是随着项目规模不断扩大，XML 的配置也逐渐增多，使得配置文件难以管理。另一方面，项目中的依赖关系越来越复杂，配置文件变得难以理解。

随着 JDK 1.5 带来的注解支持，Spring 从 2.x 开始，可以使用注解的方式来对 Bean 进行声明和注入，大大减少了 XML 的配置量。

Spring 升级到 3.x 后，提供了 JavaConfig 的能力，它可以完全取代 XML，通过 Java 代码的方式来完成 Bean 的注入。

虽然通过注解的方式来装配 Bean，可以在一定程度上减少 XML 配置带来的问题，本质问题仍然没有解决，比如：

- 依赖过多。Spring 可以整合几乎所有常用的技术框架，比如 JSON、MyBatis、Redis、Log 等，不同依赖包的版本很容易导致版本兼容问题。
- 配置过多。以 Spring 使用 JavaConfig 方式整合 MyBatis 为例，需要配置注解驱动、配置数据源、配置 MyBatis、配置事务管理器等，这些只是集成一个技术组件需要的基础配置，在一个项目中这类配置很多，开发者需要做很多类似的重复工作。
- 运行和部署很繁琐。需要先把项目打包，再部署到容器上。

### 3.1.3 Spring Boot 的价值

Spring Boot 并不是一个新的技术框架，其主要作用就是简化 Spring 应用的开发，开发者只需要通过少量的代码就可以创建一个产品级的 Spring 应用，而达到这一目的的最核心的思想就是“约定优于配置（Convention over Configuration）”。

约定优于配置（Convention over Configuration）是一种软件设计范式，目的在于减少配置的数量或者降低理解难度，从而提升开发效率。

在 Spring Boot 中，约定优于配置的思想主要体现在以下方面（包括但不限于）：

- Maven 目录结构的约定
- Spring Boot 默认的配置文件及配置文件中配置属性的约定
- 对于 Spring MVC 的依赖，自动依赖内置的 Tomcat 容器
- 对于 Starter 组件自动完成装配

Spring Boot 是基于 Spring Framework 体系来构建的，所以它并没有什么新东西，它的核心是：

- Starter 组件，提供开箱即用的组件
- 自动装配，自动根据上下文完成 Bean 的装配
- Actuator，Spring Boot 应用的监控
- Spring Boot CLI，基于命令行工具快速构建 Spring Boot 应用

其中，最核心的部分应该是自动装配，Starter 组件的核心部分也是基于自动装配来实现的。

## 3.2 快速构建 Spring Boot 应用

构建完成后，会包含以下核心配置和类：

- Spring Boot 的启动类 SpringBootDemoApplication
- resource 目录，包含 static 和 templates 目录，分别存放静态资源及前端模板，以及 application.properties 配置文件。
- Web 项目的 starter 依赖包

以往我们使用 Spring MVC 来构建一个 Web 项目需要很多基础操作：添加很多的 Jar 包依赖、在 web.xml 中配置控制器、配置 Spring 的 XML 文件或者 JavaConfig 等。而 Spring Boot 帮开发者省略了这些繁琐的基础性工作，使得开发者只需要关注业务本身，基础性的装配工作是由 Starter 组件及自动装配来完成的。

## 3.3 Spring Boot 自动装配的原理

在 Spring Boot 中，自动装配是 Starter 的基础，也是 Spring Boot 的核心。简单来说，它就是自动将 Bean 装配到 IoC 容器中，接下来，我们通过一个 Spring Boot 整合 Redis 的例子来了解一下自动装配。

- 添加 Starter 依赖

  ~~~xml
  <dependency>
  	<groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  ~~~

- 在 application.properties 中配置 Redis 的数据源：

  ~~~properties
  spring.redis.host=localhost
  spring.redis.port=6379
  ~~~

- 在 HelloController 中使用 RedisTemplate 实现 Redis 的操作：

  ~~~java
  @RestController
  public class HelloController {
      @Autowired
      RedisTemplate<String, String> redisTemplate;
      @GetMapping("/hello")
      public String hello() {
          redisTemplate.opsForValue().set("key","value");
          return "Hello World";
      }
  }
  ~~~

在这个案例中，我们并没有通过 XML 形式或者注解形式把 RedisTemplate 注入 IoC 容器中，但是在 HelloController 中却可以直接使用 @Autowired 来注入 redisTemplate 实例，这就说明，IoC 容器中已经存在 RedisTemplate。这就是 Spring Boot 的自动装配机制。

### 3.3.1 自动装配的实现

自动装配在 Spring Boot 中是通过 @EnableAutoConfiguration 注解来开启的，这个注解的声明在启动类注解 @SpringBootApplication 内。

~~~java
@SpringBootApplication
public class SpringBootDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootDemoApplication.class, args);
    }
}
~~~

进入 @SpringBootApplication 注解，可以看到 @EnableAutoConfiguration 注解的声明。

~~~java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
    @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
    @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class)
})
public @interface SpringBootApplication {
}
~~~

其实 Spring 3.1 版本就已经支持 @Enable 注解了，它的主要作用把相关组件的 Bean 装配到 IoC 容器中。@Enable 注解对 JavaConfig 的进一步完善，为使用 Spring Framework 的开发者减少了配置代码量，降低了使用的难度。比如常见的 @Enable 注解有 @EnableWebMvc、@EnableScheduling 等。

在前面的章节中讲过，如果基于 JavaConfig 的形式来完成 Bean 的装载，则必须要使用 @Configuration 注解及 @Bean 注解。而 @Enable 本质上就是针对这两个注解的封装，所以大家如果仔细关注过这些注解，就不难发现这些注解都会携带一个 @Import 注解，比如 @EnableScheduling：

~~~java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Import({SchedulingConfiguration.class})
@Documented
public @interface EnableScheduling {
}
~~~

因此，使用 @Enable 注解后，Spring 会解析到 @Import 导入的配置类，从而根据这个配置类中的描述来实现 Bean 的装配。

### 3.3.2 EnableAutoConfiguration

进入 @EnableAutoConfiguration 注解里，可以看到除 @Import 注解之外，还多了一个 @AutoConfigurationPackage 注解（它的作用是把使用了该注解的类所在的包及子包下所有组件扫描到 Spring IoC 容器中）。并且，@Import 注解中导入的并不是一个 Configuration 的配置类，而是一个 AutoConfigurationImportSelector 类。

~~~java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration
~~~

### 3.3.3 AutoConfigurationImportSelector

AutoConfigurationImportSelector 实现了 ImportSelector，它只有一个 selectImports 抽象方法，并且返回一个 String 数组，在这个数组中可以指定需要装配到 IoC 容器的类，当在 @Import 中导入一个 ImportSelector 的实现类之后，会把该实现类中返回的 Class 名称都装载到 IoC 容器中。

~~~java
public interface ImportSelector {
    String[] selectImports(AnnotationMetadata var1);
}
~~~

和 @Configuration 不同的是，ImportSelector 可以实现批量装配，并且还可以通过逻辑处理来实现 Bean 的选择性装配，也就是可以根据上下文来决定哪些类能够被 IoC 容器初始化。



通过一个简单的例子了解 ImportSelector 的使用。

- 首先创建两个类，我们需要把这两个类装配到 IoC 容器中。

  ~~~java
  public class FirstClass {}
  public class SecondClass {}
  ~~~

- 创建一个 ImportSelector 的实现类，在实现类中把定义的两个 Bean 加入 String 数组，这意味着这两个 Bean 会装配到 IoC 容器中。

  ~~~java
  public class GpImportSelector implements ImportSelector {
      @Override
      public String[] selectImports(AnnotationMetadata importingClassMetadata) {
          return new String[]{FirstClass.class.getName(), SecondClass.class.getName()};
      }
  }
  ~~~

- 为了模拟 EnableAutoConfiguration，我们可以自定义一个类似的注解，通过 @Import 导入 GpImportSelector。

  ~~~java
  @Target(ElementType.TYPE)
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Inherited
  @AutoCOnfigurationPackage
  @Import(GpImportSelector.class)
  public @interface EnableAutoImport {
  }
  ~~~

- 创建一个启动类，在启动类上使用 @EnableAutoImport 注解后，即可通过 ca.getBean 从 IoC 容器中得到 FirstClass 对象实例。

  ~~~java
  @SpringBootApplication
  @EnableAutoImport
  public class ImportSelectorMain {
      public static void main(String[] args) {
          ConfigurableApplicationContext ca = SpringApplication.run(ImportSelectorMain.class, args);
          FirstClass fc = ca.getBean(FirstClass.class);
      }
  }
  ~~~

这种实现方式相比 `@Import(*Configuration.class)` 的好处在于装配的灵活性，还可以实现批量。比如 GpImportSelector 还可以直接在 String 数组中定义多个 Configuration 类，由于一个配置类代表的是某一个技术组件中批量的 Bean 声明，所以在自动装配这个过程中只需要扫描到指定路径下对应的配置类即可。

~~~java
public class GpImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{FirstConfiguration.class.getName(), SecondConfiguration.class.getName()};
    }
}
~~~

### 3.3.4 自动装配原理分析

基于前面章节的分析可以猜想到，自动装配的核心是扫描约定目录下的文件进行解析，解析完成之后把得到的 Configuration 配置类通过 ImportSelector 进行导入，从而完成 Bean 的自动装配过程。那么接下来我们通过分析 AutoConfigurationImportSelector 的实现来求证这个猜想是否正确。

定位到 AutoConfigurationImportSelector 中的 selectImports 方法，它是 ImportSeletor 接口的实现，这个方法中主要有两个功能：

- AutoConfigurationMetadataLoader.loaderMetadata 从 META-INF/spring-autoconfigure-metadata.properties 中加载自动装配的条件元数据，简单来说就是只有满足条件的 Bean 才能够进行装配。
- 收集所有符合条件的配置类 autoConfigurationEntry.getConfigurations()，完成自动装配。

~~~java
@Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)){
        return NO_IMPORTS;
    }
    AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
    return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
~~~

需要注意的是，在 AutoConfigurationImportSelector 中不执行 selectImports 方法，而是通过 ConfigurationClassPostProcessor 中的 processConfigBeanDefinitions 方法来扫描和注册所有配置类的 Bean，最终还是会调用 getAutoConfigurationEntry 方法获得所有需要自动装配的配置类。

我们重点分析一下配置类的收集方法 getAutoConfigurationEntry，结合之前 Starter 的作用不难猜测到，这个方法应该会扫描指定路径下的文件解析得到需要装配的配置类，而这里面用到了 SpringFactoriesLoader，这块内容后续随着代码的展开再来讲解。简单分析一下这段代码，它主要做几件事情：

- getAttributes 获得 @EnableAutoConfiguration 注解中的属性 exclude、excludeName 等。
- getCandidateConfigurations 获得所有自动装配的配置类，后续会重点分析。
- removeDuplicates 去除重复的配置项。
- getExclusions 根据 @EnableAutoConfiguration 注解中配置的 exclude 等属性，把不需要自动装配的配置类移除。
- fireAutoConfigurationImportEvents 广播事件。
- 最后返回经过多层判断和过滤之后的配置类集合。

~~~java
protected AutoConfigurationEntry getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata, AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)){
        return EMPTY_ENTRY;
    }
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    configurations = removeDuplicates(configurations);
    Set<String> exclusions = getExclusions(annotationMetadata, attributes);
    checkExcludedClasses(configurations, exclusions);
    configurations.removeAll(exclusions);
    configurations = filter(configurations, autoConfigurationMetadata);
    fireAutoConfigurationImportEvents(configurations, exclusions);
    return new AutoConfigurationEntry(configurations, exclusions);
}
~~~

总的来说，它先获得所有的配置类，通过去重、exclude 排除等操作，得到最终需要实现自动装配的配置类。这里需要重点关注的是 getCandidateConfigurations ，它是获得配置类最核心的方法。

~~~java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. "
                    + "If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
~~~

这里用到了 SpringFactoriesLoader，它是 Spring 内部提供的一种约定俗成的加载方式，类似于 Java 中的 SPI。简单来说，它会扫描 classpath 下的 META-INF/spring.factories 文件，spring.factories 文件中的数据以 Key = Value 形式存储，而 SpringFactoriesLoader.loaderFactoryNames 会根据 Key 得到对应的 value 值。因此在这个场景中，Key 对应为 EnableAutoConfiguration，Value 是多个配置类，也就是 getCandidateConfigurations 方法所返回的值。

~~~properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
// 省略部分
~~~

打开 RabbitAutoConfiguration，可以看到，它就是一个基于 JavaConfig 形式的配置类。

~~~java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass({RabbitTemplate.class, Channel.class})
@EnableConfigurationProperties(RabbitProperties.class)
@Import(RabbitAnnotationDrivenConfiguration.class)
public class RabbitAutoConfiguration
~~~

除了基本的 @Configuration 注解，还有一个 @ConditionalOnClass 注解，这个条件控制机制在这里的用途是，判断 classpath 下是否存在 RabbitTemplate 和 Channel 这两个类，如果是，则把当前配置类注册到 IoC 容器。另外，@EnableConfigurationProperties 是属性配置，也就是说我们可以按照约定在 application.properties 中配置 RabbitMQ 的参数，而这些配置会加载到 RabbitProperties 中。实际上，这些东西都是 Spring 本身就有的功能。

至此，自动装配的原理基本上就分析完了，简单来总结一下核心过程：

- 通过 @Import（AutoConfigurationImportSelector）实现配置类的导入，但是这里并不是传统意义上的单个配置类装配。
- AutoConfigurationImportSelector 类实现了 ImportSelector 接口，重写了方法 selectImports，它用于实现选择性批量配置类的装配。
- 通过 Spring 提供的 SpringFactoriesLoader 机制，扫描 classpath 路径下的 META-INF/spring.factories，读取需要实现自动装配的配置类。
- 通过条件筛选的方式，把不符合条件的配置类移除，最终完成自动装配。

### 3.3.5 @Conditional 条件装配

@Conditional 是 Spring Framework 提供的一个核心注解，这个注解的作用是提供自动装配的条件约束，一般与 @Configuration 和 @Bean 配合使用。

简单来说，Spring 在解析 @Configuration 配置类时，如果该配置类增加了 @Conditional 注解，那么会根据该注解配置的条件来决定是否要实现 Bean 的装配。

#### 3.3.5.1 @Conditional 的使用

@Conditional 的注解类声明代码如下，该注解可以接收一个 Condition 的数组。

~~~java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();
}
~~~

Condition 是一个函数式接口，提供了 matches 方法，它主要提供一个条件匹配规则，返回 true 表示可以注入 Bean，反之则不注入。

~~~java
@FunctionalInterface
public interface Condition {
    boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2);
}
~~~

我们接下来基于 @Conditional 实现一个条件装配的案例。

- 自定义一个 Condition，逻辑很简单，如果当前操作系统是 Windows，则返回 true，否则返回 false。

  ~~~java
  public class GpCondition implements Condition {
      @Override
      public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
          // 此处进行条件判断，如果返回 true，表示需要加载该配置类或者 Bean
          // 否则，表示不加载
          String os = conditionContext.getEnvironment().getProperty("os.name");
          if (os.contains("Windows")) {
              return true;
          }
          return false;
      }
  }
  ~~~

- 创建一个配置类，装载一个 BeanClass （自定义的一个类）。

  ~~~java
  @Configuration
  public class ConditionConfig {
      @Bean
      @Conditional(GpCondition.class)
      public BeanClass beanClass() {
          return new BeanClass();
      }
  }
  ~~~

  在 BeanClass 的 bean 声明方法中增加 @Conditional(GpCondition.class)，其中具体的条件是我们自定义的 GpCondition 类。

  上述代码所表达的意思是，如果 GpCondition 类中的 matchs 返回 true，则将 BeanClass 装载到 Spring IoC 容器中。

#### 3.3.5.2 Spring Boot 中的 @Conditional

在 Spring Boot 中，针对 @Conditional 做了扩展，提供了更简单的使用方式，扩展注解如下：

- ConditionalOnBean / ConditionalOnMissingBean : 容器中存在某个类或者不存在某个 Bean 时进行 Bean 装载。
- ConditionalOnClass / ConditionalOnMissingClass: classpath 下存在指定类或者不存在指定类时进行 Bean 装载。
- ConditionalOnCloudPlatform： 只有运行在指定的云平台上才加载指定的 Bean。
- ConditionalOnExpression：基于 SpEl 表达式的条件判断。
- ConditionalOnJava：只有运行指定版本的 Java 才会加载 Bean。
- ConditionalOnJndi：只有指定的资源通过 JNDI 加载后才加载 Bean。
- ConditionalOnWebApplication / ConditionalOnNotWebApplication : 如果是 Web 应用或者不是 Web 应用，才加载指定的 Bean。
- ConditionalOnProperty：系统中指定的对应的属性是否有对应的值。
- ConditionalOnResource：要加载的 Bean 依赖指定资源是否存在于 classpath 中。
- ConditionalOnSingleCandidate：只有在确定了给定 Bean 类的单个候选项时才会加载 Bean。

### 3.3.6 spring-autoconfigure-metadata

除了 @Conditional 注解类，在 Spring Boot 中还提供了 spring-autoconfigure-metadata.properties 文件来实现批量自动装配条件配置。

它的作用和 @Conditional 是一样的，只是将这些条件配置放在了配置文件中。

同样这种形式也是“约定优于配置”的体现，通过这种配置化的方式来实现条件过滤必须要遵循两个条件：

- 配置文件的路径和名称必须是 /META-INF/spring-autoconfigure-metadata.properties。
- 配置文件中 key 的配置格式：自动配置类的类全路径名.条件 = 值

这种配置方法的好处在于，它可以有效地降低 Spring Boot 的启动时间，通过这种过滤方式可以减少配置类的加载数量，因为这个过滤发生在配置类的装载之前，所以它可以降低 Spring Boot 启动时装载 Bean 的耗时。

## 3.4 手写实现一个 Starter

从 Spring Boot 官方提供的 Starter 的作用来看，Starter 组件主要有三个功能：

- 涉及相关组件的 Jar 包依赖。
- 自动实现 Bean 的装配。
- 自动声明并且加载 application.properties 文件中的属性配置。

### 3.4.1 Starter 的命名规范

Starter 的命名主要分为两类，一类是官方命名，另一类是自定义组件命名。这种命名格式并不是强制性的，也是一种约定俗成的方式，可以让开发者更容易识别。

- 官方命名的格式为：spring-boot-starter-模块名称，比如 spring-boot-starter-web。
- 自定义命名格式为：模块名称-spring-boot-starter，比如 mybatis-spring-boot-starter。

简单来说，官方命名中模块名放在最后，而自定义组件中模块名放在最前面。

### 3.4.2 实现基于 Redis 的 Starter

虽然 Spring Boot 官方提供了 spring-boot-starter-data-redis 组件来实现 RedisTemplate 的自动装配，但是我们仍然基于前面学到的思想实现一个基于 Redis 简化版本的 Starter 组件。

- 创建一个工程，命名为 redis-spring-boot-starter。

- 添加 Jar 包依赖，Redisson 提供了在 Java 中操作 Redis 的功能，并且基于 Redis 的特性封装了很多可直接使用的场景，比如分布式锁。

  ~~~xml
  <dependency>
  	<groupId>org.redisson</groupId>
      <artifactId>redisson</artifactId>
      <version>3.11.1</version>
  </dependency>
  ~~~

- 定义属性类，实现在 application.properties 中配置 Redis 的连接参数，由于只是一个简单版本的 Demo，所以只简单定义了一些必要参数。另外 @ConfigurationProperties 这个注解的作用是把当前类中的属性和配置文件（properties / yml）中的配置进行绑定，并且前缀是 gp.redisson。

  ~~~java
  @ConfigurationProperties(prefix = "gp.redisson")
  public class RedissonProperties {
      private String host = "localhost";
      private String password;
      private int port = 6379;
      private int timeout;
      private boolean ssl;
      /* 省略 getter 和 setter */
  }
  ~~~

- 定义需要自动装配的配置类，主要就是把 RedissonClient 装配到 IoC 容器，值得注意的是 @ConditionalOnClass，它表示一个条件，在当前场景中表示的是：在 classpath 下存在 Redisson 这个类的时候，RedissonAutoConfiguration 才会实现自动装配。这里只演示了一种单机的配置模式，除此之外，Redisson 还支持集群、主从、哨兵等模式的配置，大家有兴趣的话可以基于当前案例去扩展，建议使用 config.fromYAML 方式，直接加载配置完成不同模式的初始化，这会比根据不太模式的判断来实现配置化的方式更加简单。

  ~~~java
  @Configuration
  @ConditionalOnClass(Redisson.class)
  @EnableConfigurationProperties(RedissonProperties.class)
  public class RedissonAutoConfiguration {
      @Bean
      RedissonClient redissonClient(RedissonProperties redissonProperties) {
          Config config = new Config();
          String prefix = "redis://";
          if (redissonProperties.isSsl()) {
              prefix = "rediss://";
          }
          SingleServerConfig singleServerConfig = config.useSingleServer()
              .setAddress(prefix + redissonProperties.getHost() + ":" + redissonProperties.getPort())
              .setConnectTimeout(redissonProperties.getTimeout());
          if (!StringUtils.isEmpty(redissonProperties.getPassword())) {
              singleServerConfig.setPassword(redissonProperties.getPassword());
          }
          return Redisson.create(config);
      }
  }
  ~~~

- 在 resources 下创建 META-INF/spring.factories 文件，使得 Spring Boot 程序可以扫描到该文件完成自动装配，key 和 value 对应如下：

  ~~~properties
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.gupaoedu.book.RedisAutoConfiguration
  ~~~

- 最后一步，使用阶段只需要做两个步骤：添加 Starter 依赖、设置属性设置：

  ~~~xml
  <dependency>
      <groupId>com.gupaoedu.book</groupId>
      <artifactId>redis-spring-boot-starter</artifactId>
      <version>1.0-SNAPSHOT</version>
  </dependency>
  ~~~

  在 application.properties 中配置 host 和 port，这个属性会自动绑定到 RedissonProperties 中定义的属性上。

  ~~~properties
  gp.redisson.host=192.168.13.106
  gp.redisson.port=6379
  ~~~

至此，一个非常简易的手写 Starter 就完成了。

# 第4章 微服务架构下的服务治理

Dubbo 是阿里巴巴内部使用的一个分布式服务治理框架，于 2012 年开源。

由于某些原因 Dubbo 在 2014 年停止了维护，所以那些使用 Dubbo 框架的公司开始自己维护，比较知名的是当当网开源的 DubboX。值得高兴的是，2017 年 9 月，阿里巴巴重启了 Dubbo 的维护并且做好了长期投入的准备，也对 Dubbo 的未来做了很多规划。2018 年 2 月份，Dubbo 进入 Apache 孵化，这意味着它将不只是阿里巴巴的 Dubbo，而是属于开源社区的，也意味着会有更多的开源贡献者参与到 Dubbo 的开发中来。

2019 年 5 月，Apache Dubbo 正式从孵化器中毕业，代表着 Apache Dubbo 正式成为 Apache 的顶级项目。

## 4.1 如何理解 Apache Dubbo

