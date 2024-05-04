# 1 工具类：
## 1.1  StringUtils__commons-lang

> `String`的进行操作的工具类
> 

> 导入依赖

```xml
<dependency>
    <groupId>commons-lang</groupId>
    <artifactId>commons-lang</artifactId>
    <version>2.6</version>
</dependency>
```

1. 将map对象转化为string并用逗号相隔开(**StringUtils.join()**)

   ```java
           List<LocalDate> dateList = new ArrayList<>();
           dateList.add(begin);
           while (!begin.equals(end)) {
               // 日期计算，计算指定日期的后一天对应日期
               begin = begin.plusDays(1);
               dateList.add(begin);
           }
   
           // 将list集合中每个元素转化成以逗号分割的字符串
           String string = StringUtils.join(dateList, ',');
   ```

2. `isNotBlank()` 不仅检查字符串是否为空，还会去除字符串两端的空格后再进行判断，只有当去除空格后的字符串不为空时才返回 true。

   ```java
   ```

   

3. `isEmpty()` ，不会去除空格字符，也不会判断字符串中是否只包含空格字符，只有当字符串为 null 或为空字符串时才返回 true。集合是通过判断长度是否为0；

   ```java
       public static boolean isEmpty(@Nullable Object str) {
           return str == null || "".equals(str);
       }
   ```

   





## 1.2 BeanUtils__commons-beanutils

> 对于类的相关操作的工具类
> 

- 导入依赖

```xml
 <!-- BeanUtils的依赖 -->
 <dependency>
     <groupId>commons-beanutils</groupId>
     <artifactId>commons-beanutils</artifactId>
     <version>1.9.4</version>
 </dependency>
```

1.  对于实体类和DTO数据转换(**BeanUtils.copyProperties()**)

```java
public void save(EmployeeDTO employeeDTO) {
    Employee employee = new Employee();
    // 对象的属性拷贝  第一个为源，第二个为目标，源拷贝到目标
    BeanUtils.copyProperties(employeeDTO, employee);
    employeeMapper.save(employeeDTO);
}
```




## 1.3 DigestUtils__springframework
- 介绍
> `Spring`的加密工具类
>
> 背景：
>
> ​	需要将**数据库中的salt+用户输入的password**字符串拼接后进行md5加密然后和数据库中的密码进行对比

- pom依赖：`springframework`

- 场景
1、对字符串进行加密处理。
```java
import org.springframework.util.DigestUtils;

String salt = dbUser.getSalt();
String password = dto.getPassword();
String pswd = DigestUtils.md5DigestAsHex((password + salt).getBytes());
if (pswd.equals(dbUser.getPassword())) {
 return ResponseResult.errorResult(AppHttpCodeEnum.LOGIN_PASSWORD_ERROR);
}
```

>  这里的salt是数据库中的，password是用户输入的，dbUser是数据库中的




# 2 JavaSE使用

## 2.1 Java8的使用

### 2.1.1 时间类







### 2.1.2 集合相关

#### 2.1.2.1 增强for（foreach）

> 增强for其实本质上就是迭代器进行遍历。
>
> 在Map或者Collection的时候，不要用它们的API直接修改集合的内容，如果要修改可以用Iterator的remove()方法

> 在Java中，`Map`和`Collection`接口（后者通过各种类如`ArrayList`、`HashSet`等实现）都是用来存储数据的结构。在遍历这些结构的过程中（例如，使用for-each循环或迭代器遍历），如果尝试直接修改集合（如添加、删除、修改（只能修改内部可以修改的）元素），则可能会引发`ConcurrentModificationException`。这个异常是Java的一种失败快速机制，用来防止在集合结构被更改时仍然进行遍历，因为这种操作可能导致不可预测的结果。
>
>**快速失败机制：**
> 
>这是因为迭代器内部维护了一个计数器，用于检测在遍历过程中集合是否被修改，如果计数器检测到集合结构发生了变化，就会抛出ConcurrentModificationException。
> 
>这个其实就是并发异常。



#### 2.1.2.2 安全删除list元素

> 4、**使用lambda表达式（推荐）**

[【JAVA】普普通通的List，写起来遇到的坑还不少！](https://www.bilibili.com/video/BV18Y4y1B7fR/?share_source=copy_web&vd_source=a9e0245042931de24eb0a8f018fa0eae)

```java
        List<String> list = new ArrayList<>();
        list.add("Apple");
        list.add("Banana");
        list.add("Banana");
        list.add("Cherry");
```

1、**使用倒叙遍历进行删除**

```java
        // 倒序遍历并删除"Banana"
        for (int i = list.size() - 1; i >= 0; i--) {
            if (list.get(i).equals("Banana")) {
                list.remove(i);
            }
        }
```

> 如果是正序遍历的话，他的`list.size()`是不停的在变化得的，所以使用倒叙。

2、**使用迭代器进行安全删除**

        // 使用迭代器来遍历并删除元素
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            String fruit = iterator.next();
            if (fruit.equals("Banana")) {
                iterator.remove(); // 安全地删除元素
            }
        }

3、**使用 Stream 的 filter 方法过滤并删除元素(推荐)**

```java
        // 使用 Stream 的 filter 方法过滤并删除元素
        list = list.stream()
                   .filter(e -> !e.equals("Banana")) // 过滤掉等于 "Banana" 的元素
                   .collect(Collectors.toList());

```

4、**使用lambda表达式（推荐）**

```java
list.removeIf(item -> item.equals("Banana"));
```

> 本质上就是迭代器删除

#### 2.1.2.3 遍历map集合

> 3、 forEach + lambda  最推荐

```java
        Map<String, String> map = new HashMap<>();
        map.put("key1", "value1");
        map.put("key2", "value2");
        map.put("key3", "value3");
```

遍历方式

```java
        // 1、 entry + stream
        map.entrySet().stream().forEach(entry ->
                System.out.println(entry.getKey() + entry.getValue())
        );
        // 2、 entry并行处理 注意线程安全
        map.entrySet().parallelStream().forEach(entry ->
                System.out.println(entry.getKey() + entry.getValue())
        );
        // 3、 forEach + lambda  最推荐
        map.forEach((k, v) -> System.out.println(k + v));
```

### 2.1.3 Steam流用法

> 背景:
>
> 这是实时计算的时候将数据存入redis中，这里是通过stream流进行处理数据

```java
//如果缓存中不存在，查询缓存中分值最小的一条数据，进行分值的比较，如果当前文章的分值大于缓存中的数据，就替换
hotArticleVoList.stream().sorted(Comparator.comparing(HotArticleVo::getScore).reversed()).collect(Collectors.toList());
```

> 上述代码片段中使用了Comparator.comparing(...).reversed()来创建一个降序排列的比较器，用于根据HotArticleVo类中的score字段值对列表进行排序。
>
> 在 Java 8 的 Stream 类中，collect 方法通常与 Collectors 类一起使用，该类提供了一系列工厂方法来创建不同的收集器（collector），用于实现对流元素的各种聚合操作
>
> 转换为集合：`如 Collectors.toList()、Collectors.toSet()` 或 `Collectors.toCollection(Supplier<C>)`



## 2.3 常用的方法

### 2.3.1 相等

- `equals` 用于检查两个字符串是否相等。

  ```java
  String str = "hello";
  if ("hello".equals(str)) // true
  ```
- `startsWith` 用于检查字符串是否以指定的前缀开始。

  ```java
  String str = "hello";
  str.startsWith("hello"); // ture
  ```

- `contains` 用于检查字符串是否包含指定的子字符串。

  ```java
  String str = "hello world";
  str.contains("hello"); // true
  ```

  

### 2.3.2 判断不为空

- `isNotBlank()` 不仅检查字符串是否为空，还会去除字符串两端的空格后再进行判断，只有当去除空格后的字符串不为空时才返回 true。

  ```java
              if(StringUtils.isNotBlank(task_json)){
                  task = JSON.parseObject(task_json, Task.class);
                  //更新数据库信息
                  updateDb(task.getTaskId(),ScheduleConstants.EXECUTED);
              }
  ```

  

- `isEmpty()` ，不会去除空格字符，也不会判断字符串中是否只包含空格字符，只有当字符串为 null 或为空字符串时才返回 true。集合是通过判断长度是否为0；

  ```java
  List<String> materials;
  if (materials != null && !materials.isEmpty()) 
  ```

  > 这里面是个集合，所以说这里需要判断是否为`null`，更安全。
  >
  > 这里分集合的和工具类`StringUtils`的`isEmpty()`  如果是集合的，就是上面的情况，如果是工具类的话直接一个就行了

  



# 3 设计模式的应用





# 4 SQL应用

## 4.1 where和on的区别

> 场景： 需要将两张表中`is_delete = 0`的数据进行整合

```sql
select * from user left join address
on user.id =address.user_id and address.is_deleted=0
where user.is deleted=0
```

> 正确的写法是被`left join on`的数据库条件写在`on`中，主表的过滤条件写在`where`中，解释如下：
>
> 数据库在关联多张表的时候需要生成一张中间临时表。
>
> - on: 在生成临时表时使用的条件，不管on中的条件是否为真，都会返回主表（left为`user`， right为`address`）的记录
> - where: 在临时表生成之后，对新的临时表进行过滤
>
> 结论：
>
> - `left join on`：左表(`user`)的过滤在where中进行，右表（`address`）的过滤在`on`中进行
> - `right join on`：与left相反（左右表不会变，其实**本质上就是附表条件在on中过滤**）
> - `inner join on`：没有区别
>
> 