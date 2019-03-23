---
title:   工厂模式
date: 2019-03-20182:12:02
tags:  
    - design-pattern
    - 创建型模式 
categories: 设计模式
---

## 概述
1. 定义:定义一个创建对象的接口,但让实现这个接口的类来决定实例化哪个类,工厂方法让类的实例化推迟到子类中进行
2. 类型:创建型 
3. 适用场景
	- 创建对象需要大量重复代码
	- 客户端(应用层)不依赖产品实例如何被创建,实现等细节
	- 一个类通过其子类来指定创建哪个对象
	- 
4. 优点
	- 用户只需要关心所需产品对应的工厂,无需关心创建细节
	- 加入新产品符合开闭原则,提高可扩展性
5. 缺点
	- 类的个数容易过多,增加复杂度
	- 增加了系统的抽象性和理解难度

## DEMO
```java
// 创建者
public abstract class VideoFactory {

    public abstract Video getVideo();

}

// 具体创建者
public class JavaVideoFactory extends VideoFactory {
    @Override
    public Video getVideo() {
        return new JavaVideo();
    }
}

public class PythonVideoFactory extends VideoFactory {
    @Override
    public Video getVideo() {
        return new PythonVideo();
    }
}

// 产品
public abstract class Video {

    public abstract void produce();

}

// 具体产品
public  class JavaVideo extends Video {

    @Override
    public void produce() {
        System.out.println("java 课程");
    }
}

public  class PythonVideo extends Video {

    @Override
    public void produce() {
        System.out.println("python 课程");
    }
}

// 调用者
public class Test {
    public static void main(String[] args) {
        VideoFactory videoFactory = new JavaVideoFactory();
        Video video = videoFactory.getVideo();
        video.produce();
        videoFactory = new PythonVideoFactory();
        video = videoFactory.getVideo();
        video.produce();
    }
}

```

## UML
工厂方法为创建统一产品等级而存在,符合开闭原则,符合里氏替换原则
![](http://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-23-factorymethod.png)

## 源码例子
1. jdk  
**collection Iterator<E> iterator()**
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-23-Iterable.png)

2. URLStreamHandlerFactory
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-23-URLStreamHandlerFactory.png)

3. ILoggerFactory


## 参考资料
图解设计模式
[设计模式](https://coding.imooc.com/class/270.html)




