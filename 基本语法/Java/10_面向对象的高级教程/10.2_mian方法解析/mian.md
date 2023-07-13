解释main方法的形式: public static void main(String[] args){}
1. main方法时虚拟机调用

2. java虚拟机需要调用类的main(方法，所以该方法的访问权限必须是public

3. java虚拟机在执行main(方法时不必创建对象，所以该方法必须是static

4. 该方法接收String类型的数组参数，该数组中保存执行java命令时传递给所
  运行的类的参数,案例演示，接收参数.

5. java 执行的程序参数1参数2参数3 [举例说明:]

  

1) 在 main()方法中，我们可以直接调用 main 方法所在类的静态方法或静态属性。 
2)  但是，不能直接访问该类中的非静态成员，必须创建该类的一个实例对象后，才能通过这个对象去访问类中的非静 态成员，[举例说明] Main01.java

```java
package com.p383_main;

public class Main01 {

​    //静态的变量/属性
​    private static  String *name* = "韩顺平教育";
​    //非静态的变量/属性
​    private int n1 = 10000;

​    //静态方法
​    public static  void hi() {
​        System.*out*.println("Main01的 hi方法");
​    }
​    //非静态方法
​    public void cry() {
​        System.*out*.println("Main01的 cry方法");
​    }

​    public static void main(String[] args) {

​        //可以直接使用 name
​        //1. 静态方法main 可以访问本类的静态成员
​        System.*out*.println("name=" + *name*);
​        *hi*();
​        //2. 静态方法main 不可以访问本类的非静态成员
​        //System.out.println("n1=" + n1);//错误
​        //cry();
​        //3. 静态方法main 要访问本类的非静态成员，需要先创建对象 , 再调用即可
​        Main01 main01 = new Main01();
​        System.*out*.println(main01.n1);//ok
​        main01.cry();
​    }
}
```









```java
public class Main02 {
    public static void main(String[] args) {
        for ( int i = 0; i < args.length; i++ ) {
            System.*out*.println("args[" + i + "] = " + args[i]);
        }
    }
}
```

