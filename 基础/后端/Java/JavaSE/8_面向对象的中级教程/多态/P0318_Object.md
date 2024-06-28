1、equals方法：

// == 判断即可以判断基本类型，又可以判断引用类型
// equals 只能判断引用类型  即所指向的空间是否相等





```java
//重写Object 的 equals方法
public boolean equals(Object obj) {
    //判断如果比较的两个对象是同一个对象，则直接返回true
    if(this == obj) {
        return true;
    }
    //类型判断
    if(obj instanceof  Person) {//是Person，我们才比较

​        //进行 向下转型, 因为我需要得到obj的 各个属性
​        Person p = (Person)obj;
​        return this.name.equals(p.name) && this.age == p.age && this.gender == p.gender;
​    }
​    //如果不是Person ，则直接返回false
​    return false;

}
```



2、hashCode方法：



1) 基本介绍 默认返回：全类名+@+哈希值的十六进制，【查看 Object 的 toString 方法】 子类往往重写 toString 方法，用于返回对象的属性信息
2) 重写 toString 方法，打印对象或拼接对象时，都会自动调用该对象的 toString 形式.
3) 案例演示：Monster [name, job, sal] 案例: ToString_.java 3) 当直接输出一个对象时，toString 方法会被默认的调用, 比如 System.out.println(monster)； 就会默认调用 monster.toString()  （这个好像需要先重写ToString）



```
//重写 toString 方法, 输出对象的属性 //使用快捷键即可 alt+insert -> toString 
@Override 

public String toString() { //重写后，一般是把对象的属性值输出，当然程序员也可以自己定制 

return "Monster{" + "name='" + name + '\'' + "

​	, job='" + job + '\'' + "

​	, sal=" + sal +

​	'}';

 }
```

