# 基础概念  

## Spring Boot的核心优势是什么？  

Spring Boot 的核心优势如下：

1. **自动配置**：根据项目中的依赖自动进行配置，减少了大量的手动配置工作。
2. **内嵌服务器**：内置 Tomcat、Jetty 等容器，应用可以直接运行为一个可执行的 JAR 包，不需要部署 WAR 包到外部服务器。
3. **简化依赖管理**：提供了一套版本管理机制，通过 Starter 简化 Maven/Gradle 的依赖配置。
4. **快速开发**：约定优于配置，开箱即用，适合快速搭建微服务项目。
5. **强大的社区和生态**：与 Spring Cloud 等组件集成良好，支持各种主流中间件和服务。
6. **生产级别的特性**：如监控、健康检查、指标收集、日志系统等一应俱全，便于部署和运维。
7. **统一的配置管理**：支持多种方式的外部化配置，方便在不同环境中部署应用。

## Spring Boot和传统Spring框架的主要区别？  

Spring Boot 与传统 Spring 框架的主要区别如下：

1. **配置方式**： 传统 Spring 需要大量的 XML 或注解配置，Spring Boot 提供自动配置，大幅减少配置量。
2. **启动方式**： Spring 需要部署到外部容器（如 Tomcat），Spring Boot 内嵌服务器，直接通过 `main` 方法运行，部署简单。
3. **依赖管理**： 传统 Spring 项目需手动管理版本，Spring Boot 提供统一的 Starter 依赖和版本控制，简化依赖配置。
4. **项目结构**： Spring Boot 提倡“约定优于配置”，项目结构和命名规范统一，开箱即用；传统 Spring 更加灵活但更繁琐。
5. **部署方式**： Spring Boot 支持打包为可执行 JAR 文件，传统 Spring 一般为 WAR 包部署。
6. **开发效率**： Spring Boot 更适合快速开发、构建微服务应用，传统 Spring 适合复杂、灵活的企业项目开发。
7. **内建功能**： Spring Boot 提供 Actuator、Metrics、Health Check、Logging 等生产级运维支持，传统 Spring 需手动集成。

总结：Spring Boot 更注重简化开发和快速部署，而传统 Spring 提供更多自定义和灵活性。

## Spring Boot的自动配置原理是怎样的？  

Spring Boot 的自动配置原理可以概括为“条件判断 + 自动装配”。具体过程如下：

1. **启动类上的 @SpringBootApplication 注解** 它是一个组合注解，包含了 `@EnableAutoConfiguration`，启用自动配置功能。
2. **@EnableAutoConfiguration 的核心机制** 它通过 `SpringFactoriesLoader` 加载 `META-INF/spring.factories` 中配置的所有自动配置类。
3. **自动配置类的本质** 每个自动配置类本质上是一个 `@Configuration` 配置类，内部使用大量 `@Conditional` 派生注解，比如：
   - `@ConditionalOnClass`：某个类存在时才加载
   - `@ConditionalOnMissingBean`：某个 Bean 不存在时才加载
   - `@ConditionalOnProperty`：某个配置项满足条件时才加载
4. **配置属性注入** 自动配置类通常会配合 `@ConfigurationProperties` 使用，把配置文件中的参数注入到配置类中。
5. **遵循用户优先** 自动配置不会覆盖用户自定义的 Bean，用户的配置优先于自动配置。

总结：Spring Boot 通过读取 classpath 中的配置类，并在满足特定条件时加载这些类，实现了“按需自动配置”的机制，大大减少了开发者的配置工作。

## 什么是Starter依赖？如何自定义Starter？  

**Starter 依赖**是 Spring Boot 提供的一种模块化依赖管理方式，用来简化常见功能的依赖引入和自动配置。

### 什么是 Starter？

Starter 是一种命名规范，通常命名为 `spring-boot-starter-xxx`，本质上是一个普通的 Maven/Gradle 项目，里面封装了一组常用依赖和可能的自动配置。

例如：

- `spring-boot-starter-web`：包含了构建 Web 应用所需的依赖，如 Spring MVC、Tomcat 等。
- `spring-boot-starter-data-jpa`：包含 JPA、Hibernate、数据库驱动等依赖。

**目的**：让开发者通过引入一个 Starter，就能获得一整套功能所需的所有依赖与配置支持。

------

### 如何自定义 Starter？

1. **创建两个模块**：

   - **starter-autoconfigure**：用于编写自动配置类。
   - **starter**：用于引入 `starter-autoconfigure` 和其它依赖，提供给用户使用。

2. **自动配置类**：

   - 使用 `@Configuration` 标注配置类。
   - 使用 `@ConditionalOnClass`、`@ConditionalOnProperty` 等控制加载条件。
   - 使用 `@ConfigurationProperties` 绑定配置。

3. **注册自动配置类**： 在 `starter-autoconfigure` 模块的 `resources/META-INF/spring.factories` 中注册配置类：

   ```
   org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
   com.example.MyAutoConfiguration
   ```

4. **打包并发布**： 将 `starter` 模块发布到私服或本地仓库，供其他项目引入。

------

总结：Starter 是 Spring Boot 的模块化依赖封装机制，自定义 Starter 的关键是提供自动配置类，并通过 `spring.factories` 注册它，让 Spring Boot 能在启动时自动识别并加载。

## Spring Boot的核心注解有哪些？  

Spring Boot 的核心注解主要包括以下几个：

1. **@SpringBootApplication**
    是一个组合注解，包含：
   - `@SpringBootConfiguration`（是 `@Configuration` 的特化，表示配置类）
   - `@EnableAutoConfiguration`（启用自动配置）
   - `@ComponentScan`（开启包扫描）
2. **@EnableAutoConfiguration**
    启用 Spring Boot 的自动配置功能，是自动装配的核心。
3. **@ConfigurationProperties**
    将配置文件中的属性与 Java Bean 进行绑定，常用于参数注入。
4. **@SpringBootConfiguration**
    标注主配置类，是 `@Configuration` 的一个特化，用于替代传统 XML 配置。
5. **@ConditionalOnClass / @ConditionalOnMissingBean / @ConditionalOnProperty 等**
    是自动配置类中常用的条件注解，用于控制配置是否生效。
6. **@RestController / @Controller / @Service / @Repository / @Component**
    Spring Boot 沿用了 Spring 的这些注解，配合自动扫描管理 Bean。

这些注解共同构建了 Spring Boot 自动配置和组件注册的基础框架，简化了开发过程。

# 配置相关  

## Spring Boot支持哪些外部化配置方式？  

Spring Boot 支持多种外部化配置方式，用于将配置从代码中分离，常见的有：

1. **配置文件**
   - `application.properties`
   - `application.yml`
      是最常用的方式，支持分环境配置（如 `application-dev.yml`）。
2. **命令行参数**
    启动应用时通过参数传入，例如：
    `--server.port=8081`，优先级高于配置文件。
3. **环境变量**
    支持从系统环境变量读取配置，如在操作系统中设置 `SERVER_PORT=8082`。
4. **JVM 启动参数**
    使用 `-D` 传入，例如：
    `-Dserver.port=8083`，同样优先级较高。
5. **配置类注解绑定**
    使用 `@ConfigurationProperties` 或 `@Value` 绑定配置值。
6. **外部配置文件路径**
    可以通过 `--spring.config.location` 或 `SPRING_CONFIG_LOCATION` 指定外部配置文件的位置。
7. **Profile 配置**
    通过 `spring.profiles.active` 激活特定环境下的配置文件。
8. **Spring Cloud Config（如果使用 Spring Cloud）**
    实现集中式配置管理，从配置中心拉取配置。

这些配置来源有优先级顺序，Spring Boot 会根据优先级加载，后者可以覆盖前者。这样就实现了灵活的配置管理。

## 如何实现多环境配置(dev/test/prod)？  

在 Spring Boot 中，实现多环境配置（如 dev、test、prod）的方法非常简单，主要通过配置文件和 Profile 来完成：

### 1. 创建多个配置文件

使用以下命名规则创建环境专属配置文件：

- `application-dev.yml`
- `application-test.yml`
- `application-prod.yml`

或者使用 `.properties` 也可以。

每个文件中定义适用于该环境的配置，例如数据库连接、端口、日志级别等。

------

### 2. 在主配置文件中设置默认环境

在 `application.yml` 或 `application.properties` 中添加：

```yaml
spring:
  profiles:
    active: dev
```

表示当前激活的是 `dev` 环境。

------

### 3. 启动时动态指定环境

也可以在启动应用时通过命令行或 JVM 参数设置环境：

- 命令行方式：
   `java -jar app.jar --spring.profiles.active=test`
- 或者 JVM 参数方式：
   `-Dspring.profiles.active=prod`

------

### 4. 使用 @Profile 注解控制 Bean 加载

对于不同环境需要加载不同 Bean 的情况，可以使用 `@Profile` 注解：

```java
@Profile("dev")
@Bean
public DataSource devDataSource() { ... }
```

这样只有在指定环境下该 Bean 才会被加载。

------

总结：通过 `application-{profile}.yml` 和 `spring.profiles.active`，Spring Boot 提供了一套优雅的多环境配置方案，支持灵活切换、隔离配置，非常适合开发、测试、生产等不同阶段的需求。

## @ConfigurationProperties和@Value的区别？  

`@ConfigurationProperties` 和 `@Value` 都是 Spring 用来从配置文件中读取属性的注解，但它们在使用方式和适用场景上有明显区别：

------

### 1. **@ConfigurationProperties**

- **用途**：绑定整个配置类，可以将一组相关的配置映射到一个 Java Bean 中。
- **写法**：基于前缀进行批量绑定。
- **特点**：
  - 支持复杂结构，如嵌套对象、集合。
  - 易于维护，适合大量配置项。
  - 支持 JSR-303 校验（如 `@Validated` 和 `@NotNull`）。
  - 通常配合 `@Component` 或通过 `@EnableConfigurationProperties` 注册。

**适用场景**：用于封装模块化配置或配置较多的情况。

------

### 2. **@Value**

- **用途**：用于注入单个配置项。
- **写法**：直接在字段或方法上写 `${}` 表达式。
- **特点**：
  - 简洁快速，适用于少量配置。
  - 不支持复杂结构绑定。
  - 不易管理，多个配置分散，不如 `@ConfigurationProperties` 易维护。

**适用场景**：注入少量或临时性配置值。

------

### 总结区别

| 特性              | @ConfigurationProperties | @Value           |
| ----------------- | ------------------------ | ---------------- |
| 绑定方式          | 批量绑定（支持前缀）     | 单个属性绑定     |
| 支持嵌套结构      | 支持                     | 不支持           |
| 可读性与维护性    | 更高                     | 较差             |
| 支持 JSR-303 校验 | 支持                     | 不支持           |
| 表达式支持        | 不支持                   | 支持 SpEL 表达式 |

实际开发中，大量配置使用 `@ConfigurationProperties`，个别简单值用 `@Value` 更高效。

## Spring Boot配置加载的优先级顺序是怎样的？  

Spring Boot 配置的加载是有明确优先级顺序的，优先级高的配置会覆盖低优先级的配置。以下是常见配置源的优先级，从高到低排列：

1. **命令行参数**（如 `--server.port=8081`）
2. **`java -D` JVM 启动参数**
3. **操作系统环境变量**
4. **`RandomValuePropertySource`（如 `${random.uuid}`）**
5. **`application-{profile}.yml/properties`**（激活的环境配置）
6. **`application.yml/properties`（默认配置文件）**
7. **`@PropertySource` 注解加载的配置文件**
8. **通过 `SpringApplication.setDefaultProperties()` 设置的默认属性**
9. **Spring Boot 默认值**（比如默认端口是 8080）

其中，Profile 配置优先于主配置文件；多个配置源存在相同键名时，优先级高的那个生效。

这套优先级体系让 Spring Boot 支持灵活的外部化配置和环境切换，符合“约定优于配置”的理念，又保留了必要的控制权。

# Web开发  

Spring Boot如何实现RESTful API？  
常用HTTP状态码及其含义？  
Spring Boot如何处理全局异常？  
Spring Boot的跨域解决方案有哪些？  
Spring Boot中过滤器(Filter)和拦截器(Interceptor)的区别？  

# 数据访问  

Spring Boot如何集成MyBatis？  
Spring Data JPA的核心功能有哪些？  
Spring Boot的事务管理方式？  
多数据源如何配置？  
Redis在Spring Boot中的常用场景？  

# 监控与运维  

Spring Boot Actuator的作用？  
常用的Actuator端点有哪些？  
如何自定义健康检查指标？  
Spring Boot应用的部署方式有哪些？  
Spring Boot应用的性能监控方案？  

## 高级特性  

Spring Boot的启动过程是怎样的？  
Spring Boot如何实现热部署？  
Spring Boot的自动配置条件判断机制？  
如何扩展Spring Boot的功能？  
Spring Boot的测试支持有哪些？  

# 微服务相关  

Spring Boot如何实现服务发现？  
Spring Cloud和Spring Boot的关系？  
Spring Boot如何实现配置中心？  
Spring Boot如何实现服务熔断？  
Spring Boot如何实现API网关？
