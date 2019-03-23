---
title:   简单工厂
date: 2019-03-20182:12:02
tags:  
    - design-pattern
    - 创建型模式 
categories: 设计模式
---

## 概述
1. 定义:由一个工厂对象决定创建出哪一种产品的实例
2. 类型:创建型,不属于GOF23
3. 适用场景
	- 工厂负责创建的对象比较少
	- 客户端只知道传入工厂参数,对于如何创建对象逻辑不关心
4. 优点
	- 只需要传入一个正确的参数,就可以获取你需要的对象,而无需知道其创建细节
5. 缺点
	- 工厂类的指责相对过重,增加新的产品,需要修改工厂类的判断逻辑,违背开闭原则

## DEMO
```java
// 抽象
public abstract class Video {

    public abstract void produce();

}

// java
public  class JavaVideo extends Video{

    @Override
    public void produce() {
        System.out.println("java 课程");
    }
}

// python
public  class PythonVideo extends Video{

    @Override
    public void produce() {

        System.out.println("python 课程");
    }
}
```

```java
// 抽象工厂
public class VideoFactory {

    public static Video getVideo(Class<? extends Video> clazz) {
        try {
            return clazz.newInstance();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        return null;
    }

}

// 调用
public class Test {
    public static void main(String[] args) {
        Video java = VideoFactory.getVideo(JavaVideo.class);
        java.produce();
        Video python = VideoFactory.getVideo(PythonVideo.class);
        python.produce();
    }
}
```
## UML
调用者调用VideoFactiory,而具体生产交个VideoFactory,按入参进行生成对象,不符合开闭原则,不符合单一职责原则
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-21-simplefactory.png)

## 源码例子
1. calendar

```java
private static Calendar createCalendar(TimeZone zone,
                                           Locale aLocale)
    {
        CalendarProvider provider =
            LocaleProviderAdapter.getAdapter(CalendarProvider.class, aLocale)
                                 .getCalendarProvider();
        if (provider != null) {
            try {
                return provider.getInstance(zone, aLocale);
            } catch (IllegalArgumentException iae) {
                // fall back to the default instantiation
            }
        }

        Calendar cal = null;

        if (aLocale.hasExtensions()) {
            String caltype = aLocale.getUnicodeLocaleType("ca");
            if (caltype != null) {
                switch (caltype) {
                case "buddhist":
                cal = new BuddhistCalendar(zone, aLocale);
                    break;
                case "japanese":
                    cal = new JapaneseImperialCalendar(zone, aLocale);
                    break;
                case "gregory":
                    cal = new GregorianCalendar(zone, aLocale);
                    break;
                }
            }
        }
        if (cal == null) {
            // If no known calendar type is explicitly specified,
            // perform the traditional way to create a Calendar:
            // create a BuddhistCalendar for th_TH locale,
            // a JapaneseImperialCalendar for ja_JP_JP locale, or
            // a GregorianCalendar for any other locales.
            // NOTE: The language, country and variant strings are interned.
            if (aLocale.getLanguage() == "th" && aLocale.getCountry() == "TH") {
                cal = new BuddhistCalendar(zone, aLocale);
            } else if (aLocale.getVariant() == "JP" && aLocale.getLanguage() == "ja"
                       && aLocale.getCountry() == "JP") {
                cal = new JapaneseImperialCalendar(zone, aLocale);
            } else {
                cal = new GregorianCalendar(zone, aLocale);
            }
        }
        return cal;
    }
```


## 参考资料
图解设计模式
[设计模式](https://coding.imooc.com/class/270.html)




