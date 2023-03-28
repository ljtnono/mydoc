# mini-spring项目学习

引言：

> 一直以来都很想学习学习spring的源代码，但是总是不知道从何下手。20年的时候，跟着刘佳老师的《spring源码深入解析》学习过一段时间，但是效果不是很理想，当时迷失在spring繁杂的类继承结构里了。偶然间看到Github上mini-spring项目，又从新燃起我学习spring源代码的热情，希望能通过这个项目对spring的设计能更深入的了解。


mini-spring项目是一个简化版的spring框架，主要实现了spring的ioc、aop等功能。为了更好的学习这个项目，自己创建了一个项目跟着作者思路一起学习，期间也会写下一些自己的心得体会。

## IOC容器的实现

### 一个简单的IOC容器实现

一个最简单的IOC容器实现，使用一个hashmap用来保存bean对象，只包含注册bean和获取bean对象两个功能。

```java

public class BeanFactory {
	private Map<String, Object> beanMap = new HashMap<>();

	public void registerBean(String name, Object bean) {
		beanMap.put(name, bean);
	}

	public Object getBean(String name) {
		return beanMap.get(name);
	}
}


```

测试：

```java

public class SimpleBeanContainerTest {

	@Test
	public void testGetBean() throws Exception {
		BeanFactory beanFactory = new BeanFactory();
		beanFactory.registerBean("helloService", new HelloService());
		HelloService helloService = (HelloService) beanFactory.getBean("helloService");
		assertThat(helloService).isNotNull();
		assertThat(helloService.sayHello()).isEqualTo("hello");
	}

	class HelloService {
		public String sayHello() {
			System.out.println("hello");
			return "hello";
		}
	}
}

```

