# Spring

## 注解

### @PostConstruct

https://www.cnblogs.com/fnlingnzb-learner/p/10758848.html

Spring的@PostConstruct注解在方法上，表示此方法是在Spring实例化该Bean之后马上执行此方法，之后才会去实例化其他Bean，并且一个Bean中@PostConstruct注解的方法可以有多个。

注：@PostConstruct在依赖注入（例如@Autowired）之后执行

Constructor >> @Autowired >> @PostConstruct



### @RequestParam、@QueryParam等Spring常见参数注解区别

https://blog.csdn.net/shipfei_csdn/article/details/103157720

## 问题

### 如何在静态方法或非Spring Bean中注入Spring Bean

#### 方法1：

https://my.oschina.net/zhangxufeng/blog/1629027

https://blog.csdn.net/baidu_30809315/article/details/78920647

https://blog.csdn.net/qq_42694534/article/details/103292852



 Spring提供了两个接口：BeanFactoryAware和ApplicationContextAware，这两个接口都继承自Aware接口。如下是这两个接口的声明：

```java
public interface BeanFactoryAware extends Aware {
	void setBeanFactory(BeanFactory beanFactory) throws BeansException;
}

public interface ApplicationContextAware extends Aware {
	void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
}
```

 在Spring官方文档中描述，在初始化Spring bean时，如果检测到某个bean实现了这两个接口中的一个，那么就会自动调用该bean所实现的接口方法



```java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

/**
 * Content:普通类注入
 * @auther: wha
 * @Date: 2019/7/17 9:51
 */
@Component
public class SpringContextUtils implements ApplicationContextAware {

    /**
     * 上下文对象实例
     */
    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        SpringContextUtils.applicationContext = applicationContext;
    }

    /**
     * 获取applicationContext
     *
     * @return
     */
    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }

    /**
     * 通过name获取 Bean.
     *
     * @param name
     * @return
     */
    public static Object getBean(String name) {
        return getApplicationContext().getBean(name);
    }

    /**
     * 通过class获取Bean.
     *
     * @param clazz
     * @param <T>
     * @return
     */
    public static <T> T getBean(Class<T> clazz) {
        return getApplicationContext().getBean(clazz);
    }

    /**
     * 通过name,以及Clazz返回指定的Bean
     *
     * @param name
     * @param clazz
     * @param <T>
     * @return
     */
    public static <T> T getBean(String name, Class<T> clazz) {
        return getApplicationContext().getBean(name, clazz);
    }
}

这样就在其他地方调用静态方法从容器中获取对象了

/**
 * Content:
 *
 * @auther: wha
 * @Date: 2019/11/7 10:27
 */
public class SendMessager {
    private static Logger logger= LoggerFactory.getLogger(SendMessager.class);

    private static RedisService redisService = SpringContextUtils.getBean(RedisServiceImpl.class);

    private static TemplateCodeConfigMapper codeConfigMapper = SpringContextUtils.getBean(TemplateCodeConfigMapper.class);

    private static TemplateCodeConfig getTemplateCodeConfig(Integer id) {
        TemplateCodeConfig templateCodeConfig = codeConfigMapper.selectByPrimaryKey(id);
        return templateCodeConfig;
    }
    public static ReturnData message(){  //静态方法
		TemplateCodeConfig templateCodeConfig = getTemplateCodeConfig(type);
		return ReturnData.success(templateCodeConfig);
   }
}
```

#### 方法2

```java
@Component
public class ServiceContextUtils implements ApplicationListener<ContextRefreshedEvent> {

    public static RedisService redisService;

    @Override
    public void onApplicationEvent(ContextRefreshedEvent contextRefreshedEvent) {
        //root context
        if (contextRefreshedEvent.getApplicationContext().getParent() == null) {
            ApplicationContext context = contextRefreshedEvent.getApplicationContext();
            ServiceHolder.redisService = context.getBean(RedisService.class);
        }
    }
}

然后直接调用ServiceContextUtils.redisService即可
```

# HTTP

## httpReqeuset

request.getRequestURL() 返回全路径

request.getRequestURI() 返回除去host（域名或者ip）部分的路径

request.getContextPath() 返回工程名部分，如果工程映射为/，此处返回则为空

request.getServletPath() 返回除去host和工程名部分的路径

例如：
request.getRequestURL() http://localhost:8080/jqueryLearn/resources/request.jsp 
request.getRequestURI() /jqueryLearn/resources/request.jsp
request.getContextPath()/jqueryLearn 
request.getServletPath()/resources/request.jsp 

注： resources为WebContext下的目录名 
   jqueryLearn 为工程名

# java工具

jps:查看正在运行的java进程

jstack：jstack 28477 > test.txt 将jstack信息输出到指定文件

分析线程状态信息

grep java.lang.Thread.State test.txt | awk '{print $2$3$4$5}' | sort | uniq -c

grep java.lang.Thread.State test.txt | awk '{print $2$3$4$5}' | uniq -c



tomcat:

tomcat“闪退” 进程终止排查-进程退出、解决方案:

https://blog.csdn.net/hatsune_miku_/article/details/73301921?%3E



# 网站



Thread类中的方法：join()、sleep()、yield()之间的区别  https://blog.csdn.net/xzp_12345/article/details/81129735



[条件变量的await()释放锁吗？](https://segmentfault.com/q/1010000008606859)

[java 线程 Lock 锁使用Condition实现线程的等待（await）与通知(signal)](https://www.cnblogs.com/jalja/p/5895051.html)

java主线程结束和子线程结束之间的关系 https://blog.csdn.net/aitangyong/article/details/16858273

java多线程系列之synchronousQueue https://juejin.im/post/6844903506789269518



利用virsh和xml文件创建虚拟机  https://blog.csdn.net/qq_15437629/article/details/77827033

kvm libvirt qemu实践系列(二)-虚拟机管理 https://opengers.github.io/virtualization/kvm-libvirt-qemu-2/

kvm libvirt qemu实践系列(三)-虚拟机xml文件使用 https://opengers.github.io/virtualization/kvm-libvirt-qemu-3/

qemu-img 的使用 https://blog.csdn.net/qq_33932782/article/details/54896619

KVM中管理存储池（创建和删除） https://blog.51cto.com/xiangcun168/1680498

[不要再问我跨域的问题了](https://segmentfault.com/a/1190000015597029)

[java中静态代码块的用法 static用法详解](https://www.cnblogs.com/panjun-Donet/archive/2010/08/10/1796209.html)

跟我一起剖析 Java 并发源码之 Unsafe https://juejin.im/post/6844903478813261832

Java ArrayList add(index,element) 方法插入元素到数组指定位置  https://blog.csdn.net/ForeverCjl/article/details/12566311

java.io.File类中mkdir()与mkdirs()区别https://blog.csdn.net/a_woxinfeiyang_a/article/details/51419980

https://unbug.github.io/codelf/#Stand 变量取名

javax.ws.rs注解：@Conumes 和 @Produces等  https://blog.csdn.net/zhengchao1991/article/details/54375626

@Path注解 https://my.oschina.net/niceyoo/blog/4262417



request.getcontextPath() 详解  https://blog.csdn.net/haojiahj/article/details/8796472

[SpringBoot之二：部署Spring Boot应用程序方式](https://www.cnblogs.com/duanxz/p/3556012.html)

[SpringBoot中通过SpringBootServletInitializer如何实现容器初始化](https://www.cnblogs.com/duanxz/p/3557823.html)

Spring Boot 之FilterRegistrationBean --支持web Filter 排序的使用  https://blog.csdn.net/doctor_who2004/article/details/56055505

don't flush the Session after an exception occurs异常 https://blog.csdn.net/cwlacxm/article/details/11779449

Jackson中的 null 处理 https://doboot.github.io/2015/10/27/jackson-zhong-de-null-chu-li/

IDEA开发软件在linux环境下使用搜狗输入法无法进行中文输入 https://my.oschina.net/NatureKingShine/blog/3197280

修改centos7.3输入法切换快捷键 https://jingyan.baidu.com/article/fec7a1e5c4bd1c1191b4e710.html

[自己动手实现简单权限控制](https://www.cnblogs.com/java-class/p/6344191.html)

Java 线程安全的集合 https://codexiaomai.github.io/posts/89530ef9.html

Java CAS 原理剖析 https://juejin.im/post/6844903558937051144

自定义 RestTemplate 异常处理 https://ethendev.github.io/2018/11/06/RestTemplate-error-handler/

并发包中ThreadLocalRandom类原理剖析 http://ifeve.com/%E5%B9%B6%E5%8F%91%E5%8C%85%E4%B8%ADthreadlocalrandom%E7%B1%BB%E5%8E%9F%E7%90%86%E5%89%96%E6%9E%90/

Spring 的监听事件 ApplicationListener 和 ApplicationEvent 用法 https://blog.csdn.net/ilovejava_2010/article/details/7953419

通过Spring ApplicationListener监听器触发事件  https://blog.csdn.net/fuck487/article/details/104631263

java多线程（十）使用线程安全的集合 https://blog.csdn.net/u011277123/article/details/80161654

Java中Set的contains()方法  https://blog.csdn.net/violet_echo_0908/article/details/50152915

图说Java —— 理解Java机制最受欢迎的8幅图 https://blog.csdn.net/renfufei/article/details/13594715

