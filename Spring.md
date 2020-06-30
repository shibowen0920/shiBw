#### Spring

1. Bean的生命周期
   - 实例化bean对象（通过构造方法/工厂方法）
   - 设置对象属性（依赖注入）
   - 调用BeanNameAware方法setBeanName
   - 调用BeanFactoryAware方法setbeanFactory
   - 调用ApplicationContextAware的setApplicationContext方法
   - 调用BeanPostProcess的前置方法
   - 调用intializingBean的afterPropertiesSet
   - 调用定制初始化方法
   - 调用BeanPostProcess的后置方法
   - bean准备就绪
   - disposableBean的destory方法

2. Bean的作用域

   - singleton
     - 在IoC容器中仅存在一个Bean实例，Bean以单例方式存在，默认值

   - prototype
     - 每次从容器中调用Bean时，都返回一个新的实例，即每次调用getBean()时，相当于执行new xxxBean()

   - request
     - 每次http请求都会创建一个新的Bean，该作用域仅适用于WebApplicationContext环境

   - session
     - 同一个http session共享一个Bean，不同session使用不同Bean，仅适用于WebapplicationContext环境

   - globalSession
     - 一般用户portlet应用环境，该作用域仅适用于WebapplicationContext环境

3. SpringMVC工作流程
   - 用户向服务端发起请求，请求会先到前端控制器DispatcherServlet
   - DispatcherServlet接收到请求后会调用HandlerMapping处理器映射器，由此得知这个请求应该由哪个controller来处理（并未调用controller，只是得知）
   - DispatcherServlet调用HandlerAdapter处理器，告诉处理器适配器应该去执行哪个controller
   - HandlerAdapter处理器适配器去执行controller，得到ModelAndView（数据和视图），并层层返回给DispatcherServlet
   - DispatcherServlet将ModelAndView交给ViewReslover视图解析器解析，然后返回真正的视图。
   - DispatcherServlet将模型数据填充到视图中
   - DispatcherServlet将结果响应给用户
   - ![image-20200529144652408](C:\Users\aaa\AppData\Roaming\Typora\typora-user-images\image-20200529144652408.png)

4. Spring的IOC和DI

   - IoC是什么
     Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。如何理解好Ioc呢？理解好Ioc的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：

   - 谁控制谁，控制什么：

     传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对象的创建；谁控制谁？当然是IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象包括比如文件等）。

   - 为何是反转，哪些方面反转了：

     有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。
     用图例说明一下，传统程序设计如下图1，都是主动去创建相关对象然后再组合起来：

     ![img](https://img-blog.csdn.net/20170517200304383) 

     当有了IoC/DI的容器后，在客户端类中不再主动去创建这些对象了，如图2所示：

     ![img](https://img-blog.csdn.net/20170517200447995)

   - IoC能做什么

     IoC不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能指导我们如何设计出松耦合、更优良的程序。传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合，难于测试；有了IoC容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。
     其实IoC对编程带来的最大改变不是从代码上，而是从思想上，发生了“主从换位”的变化。应用程序原本是老大，要获取什么资源都是主动出击，但是在IoC/DI思想中，应用程序就变成被动的了，被动的等待IoC容器来创建并注入它所需要的资源了。
     IoC很好的体现了面向对象设计法则之一 —— 好莱坞法则：“别找我们，我们找你”；即由IoC容器帮对象找相应的依赖对象并注入，而不是由对象主动去找。

   - IoC和DI

     DI—Dependency Injection，即“依赖注入”：是组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

     理解DI的关键是：“谁依赖谁，为什么需要依赖，谁注入谁，注入了什么”，那我们来深入分析一下：

     谁依赖于谁：当然是应用程序依赖于IoC容器；
     为什么需要依赖：应用程序需要IoC容器来提供对象需要的外部资源；
     谁注入谁：很明显是IoC容器注入应用程序某个对象，应用程序依赖的对象；
     注入了什么：就是注入某个对象所需要的外部资源（包括对象、资源、常量数据）。

     IoC和DI有什么关系呢？其实它们是同一个概念的不同角度描述，由于控制反转概念比较含糊（可能只是理解为容器控制对象这一个层面，很难让人想到谁来维护对象关系），所以2004年大师级人物Martin Fowler又给出了一个新的名字：“依赖注入”，相对IoC 而言，“依赖注入”明确描述了“被注入对象依赖IoC容器配置依赖对象”。
     

5. 
6. `BeanFactory`和`ApplicationContext`的区别在于，`BeanFactory`的实现是按需创建，即第一次获取Bean时才创建这个Bean，而`ApplicationContext`会一次性创建所有的Bean。实际上，`ApplicationContext`接口是从`BeanFactory`接口继承而来的，并且，`ApplicationContext`提供了一些额外的功能，包括国际化支持、事件和通知机制等。通常情况下，我们总是使用`ApplicationContext`，很少会考虑使用`BeanFactory`。

