---
title: 设计原则
date: 2019-03-20182:12:02
tags:  design-pattern 
categories: 设计模式
---

# 设计原则
## 开放封闭原则（Open Closed Principle - OCP)
### 介绍
1. 原文：Software entities like classes, modules and functions should be open for extension but closed for modifications.
2. 译文：软件实体，如：类、模块与函数，对于扩展应该是开放的，但对于修改应该是封闭的。
3. 理解：简言之，对扩展开放，对修改封闭。换句话说，可以去扩展类，但不要去修改类。
4. 应用：当需求有改动，要修改代码了，此时您要做的是，尽量用继承或组合的方式来扩展类的功能，而不是直接修改类的代码。当然，如果能够确保对整体架构不会产生任何影响，那么也没必要搞得那么复杂了，直接改这个类吧。

### demo
#### 基本代码  

```java

public interface ICourse {

    Integer getId();

    String getName();

    Double getPrice();
}


public class JavaCourse implements ICourse {

    private Integer id;
    private String name;

    private Double price;

    public JavaCourse(Integer id, String name, Double price) {
        this.id = id;
        this.name = name;
        this.price = price;
    }

    @Override
    public Integer getId() {
        return id;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public Double getPrice() {
        return price;
    }

}
```

#### refactor(加入打折课程)

```java
public class JavaDiscountCourse extends JavaCourse {

    public JavaDiscountCourse(Integer id, String name, Double price) {
        super(id, name, price);
    }


    public Double getDisCountPrice() {
        return super.getPrice() * 0.8;
    }
}

```
#### uml
新的需求加入打折课程,并不修改原有的类,而是使用继承的方式进行扩展,这也就是开闭原则所说的对修改关闭,对扩展开放
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-21-openclose-1.png)

## 依赖倒置原则
### 介绍
1. 原文：High level modules should not depends upon low level modules. Both should depend upon abstractions. Abstractions should not depend upon details. Details should depend upon abstractions.
2. 译文：高层模块不应该依 于抽象。抽象不应该依赖于细节，细节应该依赖于抽象。
3. 理解：应该面向接口编程，不应该面向实现类编程。面向实现类编程，相当于就是论事，那是正向依赖（正常人思维）；面向接口编程，相当于通过事物表象来看本质，那是反向依赖，即依赖倒置（程序员思维）。
4. 应用：并不是说，所有的类都要有一个对应的接口，而是说，如果有接口，那就尽量使用接口来编程吧。

### demo
#### origin 
**学习课程**

```java
public class Evan {


    public void studyJavaCourse() {
        System.out.println("evan study java");
    }

    public void studyFECourse() {
        System.out.println("evan study FE");
    }

    public void studyPythonCourse() {
        System.out.println("evan study python");
    }
}


public class Test {

    public static void main(String[] args) {
        Evan evan = new Evan();
        evan.studyFECourse();
        evan.studyJavaCourse();
        evan.studyPythonCourse();
    }
}
```

**UML如下图,可以看到Test依赖于具体的实现,当Test加入新的需求,需要则Evan类的具体实现加入新的功能,也就意味着Evan类是不稳定的,是随时发生变化的**  
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-21-dependenceinversion.png)

#### refactor

```java
// 底层接口
public interface ICourse {

    void studyCourse();
}

// 底层接口实现
public class FeCourse implements ICourse{
    @Override
    public void studyCourse() {
        System.out.println("evan study FE");
    }
}

public class JavaCourse implements ICourse{
    @Override
    public void studyCourse() {
        System.out.println("evan study java");
    }
}

// 中层使用者
public class Evan {

    private ICourse course;

    public void setCourse(ICourse course) {
        this.course = course;
    }

    public void studyCourse() {
        course.studyCourse();
    }

}

// 顶层调用者
public class Test {


    public static void main(String[] args) {
        Evan evan = new Evan();

        evan.setCourse(new JavaCourse());
        evan.studyCourse();
        evan.setCourse(new FeCourse());
        evan.studyCourse();
    }
}

```

**UML如下:现在将课程抽象成接口,位于最底层,中层用Evan类作为桥梁注入接口类作为一个属性,顶层调用者觉得具体接口实现,现在当新加入课程学习的需求时,除Test调用者以外,所有的类都不需要进行变化,只需要扩展接口的实现,这样的架构相比于之前的方案是稳定的,这也就是面向接口编程,OOP的多态特性优势的体现**
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-21-dependenceinversion-1.png)

## 单一指责原则
### 含义
1. 原文：There should never be more than one reason for a class to change.
2. 译文：永远不应该有多于一个原因来改变某个类。
3. 理解：对于一个类而言，应该仅有一个引起它变化的原因。说白了就是，不同的类具备不同的职责，各施其责。这就好比一个团队，大家分工协作，互不影响，各做各的事情。
4. 应用：当我们做系统设计时，如果发现有一个类拥有了两种的职责，那就问自己一个问题：可以将这个类分成两个类吗？如果真的有必要，那就分吧。千万不要让一个类干的事情太多！

### demo
**可以理解为Object单一指责,接口单一指责,方法单一指责,以下例子为违反单一指责**

```java
private void doingsomething(boolean b) {
        if (b) {
            //todo something1
        } else {
            //todo something2
        }
    }
```




## 接口隔离原则
### 含义
1. 原文：The dependency of one class to another one should depend on the smallest possible interface.
2. 译文：一个类与另一个类之间的依赖性，应该依赖于尽可能小的接口。
3. 理解：不要对外暴露没有实际意义的接口。也就是说，接口是给别人调用的，那就不要去为难别人了，尽可能保证接口的实用性吧。她好，我也好。
4. 应用：当需要对外暴露接口时，需要再三斟酌，如果真的没有必要对外提供的，就删了吧。一旦您提供了，就意味着，您将来要多做一件事情，何苦要给自己找事做呢。

### demo
#### origin
**原始animal接口的实现,如果有Dog类实现此接口,必须实现fly方法,说明是不合理的,需要进行重构**

```java
public interface IAnimalAction {
    void eat();

    void fly();

    void swim();
}

```
#### refactor
**接口隔离**

```java
// 接口
public interface IEatAnimalAction {
    void eat();
}

public interface IFlyAnimalAction {
    void fly();
}

public interface ISwimAnimalAction {
    void swim();
}
// 实现
public class Bird implements IEatAnimalAction,IFlyAnimalAction{
    @Override
    public void eat() {

    }
    @Override
    public void fly() {

    }
}

public class Dog implements IEatAnimalAction,ISwimAnimalAction{
    @Override
    public void eat() {

    }

    @Override
    public void swim() {

    }
}

```

**UML**
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-21-Interfacesegregation.png)
## 迪米特原则(最小知道原则)
### 含义
1. 原文：Only talk to you immediate friends.
2. 译文：只与你最直接的朋友交流。
3. 理解：尽量减少对象之间的交互，从而减小类之间的耦合。简言之，一定要做到：低耦合，高内聚。
4. 应用：在做系统设计时，不要让一个类依赖于太多的其他类，需尽量减小依赖关系，否则，您死都不知道自己怎么死的。

### demo
**方法的入参为朋友,方法内引用为非朋友,尽量只与朋友交流**  

```java

public class Boss {

    public void commandCheckNumber(TeamLeader teamLeader) {

        teamLeader.checkNumberOfCourse();
    }
}


public class TeamLeader {


    public void checkNumberOfCourse(){
        List<Course> courseList = new ArrayList<>();
        for (int i = 0; i < 20; i++) {
            courseList.add(new Course());
        }
        System.out.println("course size"+courseList.size());
    }
}

public class Course {
}
```

**UML:Boss仅仅与teamleader交流,不与Course交流**
![](https://evanljp-blog.oss-cn-beijing.aliyuncs.com/2019-03-21-demeter.png)


## 里氏替换原则
1. 原文：Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it.
2. 译文：使用基类的指针或引用的函数，必须是在不知情的情况下，能够使用派生类的对象。
3. 理解：父类能够替换子类，但子类不一定能替换父类。也就是说，在代码中可以将父类全部替换为子类，程序不会报错，也不会在运行时出现任何异常，但反过来却不一定成立。
4. 应用：在继承类时，务必重写（Override）父类中所有的方法，尤其需要注意父类的 protected 方法（它们往往是让您重写的），子类尽量不要暴露自己的 public 方法供外界调用。往往不覆盖父类提供的方法,仅仅增加方法.
5. 含义:
    * 子类可以实现父类的抽象方法,但是不能覆盖父类的非抽象方法.
    * 子类可以增加自己的特有方法.
    * 当子类的方法重载父类的方法时,方法的前置条件(即方法的输入以及入参)要比父类的输入参数更加宽松.
    * 当子类的方法实现父类的方法时(重写,重载,实现抽象方法时),方法的后置条件(输出以及返回值)要比父类更加严格或者相等.
## 合成聚合复用原则
1. 定义:尽量使用对象组合/聚合,而不是继承关系实现软件复用的关系,聚合:has a,组合:contains a
2. 优点:可以使系统更加灵活,降低类之间的耦合度,一个类的变化对其他类的影响程度比较小

*参考:[设计模式](https://coding.imooc.com/class/270.html)*




