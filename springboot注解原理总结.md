#### springboot注解原理总结

***字典：**

Configuration——配置

EnableAutoConfiguration——启用启动配置

Registrar——注册者，注册机构，登记机关

Selector——选择器

Deferred——延迟

***

- @SpringBootApplication——一个组合注解
  - @SpringBootConfiguration
    - 这个注解除了元注解以外，只有一个@Configuration。他能让我们去注册一些额外的Bean，并且导入一些额外的配置。
    - 把该类变成一个配置类，无需额外进行xml配置。@SpringBootConfiguration就相当于@Configuration。@Configuration的核心是@Component，说明spring的配置类也是spring的一个组件。
  - @EnableAutoConfiguration
    - @EnableAutoConfiguration是由@AutoConfigurationPackage和@Import(EnableAutoConfigurationImportSelector.class)组成。
      - @AutoConfigurationPackage
        - 让包中的类以及子包中的类能够被自动扫描到spring容器中。
        - @Import({Registrar.class})给Spring容器中导入一个组件，通过这个组件获取扫描的包路径。
        - metadata就是主配置类application，其实就是将主配置类所在的包及子包里面所有组件扫描加载到spring容器，所以要将DemoApplication放在项目的最高级中（最外层目录）。
      - @Import(AutoConfigurationImportSelector.class)
        - AutoConfigurationImportSelector继承了DeferredImportSelector、继承了ImportSelector。ImportSelector有一个方法为：selectImports。
          将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中。
          会给容器中导入非常多的自动配置类（xxxAutoConfiguration），就是给容器中导入这个场景需要的所有组件，并配置好这些组件。
          有了自动配置类，免去了我们手动编写配置注入功能组件等的工作。
        - getCandidateConfigurations()这个方法，他的作用就是引入系统已经加载好的一些类，会从META-INF/spring.factories中获取资源，然后通过Properties加载资源。
  - @ComponentScan
    - @ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class), @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })，这个注解就是扫描包，然后放入spring容器。
  - 总结：
    - 他已经把很多东西准备好，具体是否使用取决于我们的程序或者说配置。

***

- run方法——一个spring的bean的加载过程。
- stater——引入springboot-autoconfigrue。
  - 通过配置文件设定好，用@ConfigurationProperties读取配置文件。
  - 然后我们在通过spring.factories也来进行配置。

***

- @ComponentScan注解的作用是扫描@SpringBootApplication所在的Application类（即spring-boot项目的入口类）所在的包（basepackage）下所有的@component注解（或拓展了@component的注解）标记的bean，并注册到spring容器中。

- @EnableAutoConfiguration注解加载的是资源目录META-INF文件下的spring.factories的文件。包括导入到项目中的Jar包的META-INF文件夹下的spring.factories文件。spring.factories文件是一个properties文件。