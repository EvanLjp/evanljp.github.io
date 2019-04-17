---
title:   抽象工厂
date: 2019-03-20182:12:02
tags:  
    - design-pattern
    - 创建型模式 
categories: 设计模式
---

## 概述
1. 定义:抽象工厂提供一个创建一系列相关或者相互依赖对象的接口,无需指定被创建者具体类
2. 类型:创建型 
3. 适用场景
	- 客户端(应用层)不依赖产品实例如何被创建,实现等细节
	- 强调一系列相关的产品对象(同一产品族) 一起使用创建对象需要大量重复代码
	- 提供了一个产品类的库,所有产品以同样接口出现,从而使客户端不依赖与具体的实现
4. 优点
    - 具体的产品在应用层代码隔离,无需关系创建细节
    - 将一系列产品族统一到一起进行创建
    
5. 缺点
	- 规定了所有可能被创建产品的集合,产品族扩展新的产品比较困难,需要修改抽象工厂的接口  
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




