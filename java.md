# Spring

## 注解

### @PostConstruct

https://www.cnblogs.com/fnlingnzb-learner/p/10758848.html

Spring的@PostConstruct注解在方法上，表示此方法是在Spring实例化该Bean之后马上执行此方法，之后才会去实例化其他Bean，并且一个Bean中@PostConstruct注解的方法可以有多个。

注：@PostConstruct在依赖注入（例如@Autowired）之后执行

Constructor >> @Autowired >> @PostConstruct



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

