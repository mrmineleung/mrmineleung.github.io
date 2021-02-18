---
layout: post
title:  "Invoke a Java method by given method name"
date:   2020-11-03 11:51:37 +0800
categories: Java
tags: 
  - "Java"
  - "Invoke Method"
---

For those who want a straight-forward code example in Java 7:

Dog class:
{% highlight java %}
package com.mypackage.bean;

public class Dog {
    private String name;
    private int age;

    public Dog() {
        // empty constructor
    }

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void printDog(String name, int age) {
        System.out.println(name + " is " + age + " year(s) old.");
    }
}
{% endhighlight %}
ReflectionDemo class:
{% highlight java %}
package com.mypackage.demo;

import java.lang.reflect.*;

public class ReflectionDemo {

    public static void main(String[] args) throws Exception {
        String dogClassName = "com.mypackage.bean.Dog";
        Class<?> dogClass = Class.forName(dogClassName); // convert string classname to class
        Object dog = dogClass.newInstance(); // invoke empty constructor

        String methodName = "";

        // with single parameter, return void
        methodName = "setName";
        Method setNameMethod = dog.getClass().getMethod(methodName, String.class);
        setNameMethod.invoke(dog, "Mishka"); // pass arg

        // without parameters, return string
        methodName = "getName";
        Method getNameMethod = dog.getClass().getMethod(methodName);
        String name = (String) getNameMethod.invoke(dog); // explicit cast

        // with multiple parameters
        methodName = "printDog";
        Class<?>[] paramTypes = {String.class, int.class};
        Method printDogMethod = dog.getClass().getMethod(methodName, paramTypes);
        printDogMethod.invoke(dog, name, 3); // pass args
    }
}
{% endhighlight %}
Output: Mishka is 3 year(s) old.

You can invoke the constructor with parameters this way:
{% highlight java %}
Constructor<?> dogConstructor = dogClass.getConstructor(String.class, int.class);
Object dog = dogConstructor.newInstance("Hachiko", 10);
{% endhighlight %}
Alternatively, you can remove
{% highlight java %}
String dogClassName = "com.mypackage.bean.Dog";
Class<?> dogClass = Class.forName(dogClassName);
Object dog = dogClass.newInstance();
{% endhighlight %}
and do
{% highlight java %}
Dog dog = new Dog();

Method method = Dog.class.getMethod(methodName, ...);
method.invoke(dog, ...);
{% endhighlight %}

[FYI][FYI-Link]

[FYI-Link]: https://stackoverflow.com/questions/160970/how-do-i-invoke-a-java-method-when-given-the-method-name-as-a-string