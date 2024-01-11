## 一、前言

前几个月我跳槽，入职了一家软件[外包](https://so.csdn.net/so/search?q=外包&spm=1001.2101.3001.7020)公司。虽然薪资很低，但好在不用加班。项目是个外国的，给我最大的感觉就是老外很重视UT，覆盖率要80%以上。所以开发工作中写UT也是很重要的工作。

由于我之前待过的几家公司是民企，对UT并不重视，而且我个人也没有特地学UT。虽然从大学就接触JUnit了，但是只停留在会用@Test这个水平，现在是时候学习下JUnit了。因为目前新的SpringBoot用的JUnit5，所以直接看JUnit5，JUnit4和JUnit5有不少差异。

当然UT工具不仅仅是JUnit，SpringBoot还集成了mockito、hamcrest等工具。

### 1. 引入test包

现在的项目基本使用SpringBoot，所以直接引入spring-boot-starter-test，

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
12345
```

## 二、注解

| 注解                   | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| @SpringBootTest        | SpringBoot的注解，修饰在类上，程序会找到@SpringBootApplication修饰的主启动类，创建ApplicationContext容器。 |
| @ExtendWith            | （1）当你测试类用到Spring时： Spring boot 2.1.x之前，@SpringBootTest 需要配合@ExtendWith(SpringExtension.class)才能正常工作的。 Spring Boot 2.1.x之后，@SpringBootTest里包含了@ExtendWith(SpringExtension.class)，就无需再加@ExtendWith注解。 （2）当你测试类不用Spring时： 如果您只想涉及Mockito而不必涉及Spring，那么当您只想使用@Mock、@InjectMocks注解时，您就得使用@ExtendWith(MockitoExtension.class)，因为它不会加载到很多不需要的Spring东西中。 |
| @Test                  | 修饰在方法上，表示这个方法是一个测试方法                     |
| @ParameterizedTest     | 修饰在方法上，进行参数化测试。对同一个测试方法，可以有多种入参对其测试，要定义一个参数源。 |
| @RepeatedTest          | 修饰在方法上，表示会自动重复测试这个方法，比如@RepeatedTest(10)，会自动执行10次。 |
| @DisplayName           | 修饰在类或者方法上，指定测试方法的展示名称                   |
| @DisplayNameGeneration | 修饰在类上，展示名称生成器。设置一定规则生成方法的展示名称。 例如：方法if_it_is_zero()展示出的名字为if it is zero，去掉了其中的下划线。 |
| @BeforeEach            | 修饰在方法上，在每一个测试方法（所有@Test、@RepeatedTest、@ParameterizedTest或者@TestFactory注解的方法）之前执行一次。 例如：一个测试类有2个测试方法testA()和testB()，还有一个@BeforeEach的方法，执行这个测试类，@BeforeEach的方法会在testA()之前执行一次，在testB()之前又执行一次。@BeforeEach的方法一共执行了2次。 |
| @AfterEach             | 修饰在方法上，和@BeforeEach对应，在每一个测试方法（所有@Test、@RepeatedTest、@ParameterizedTest或者@TestFactory注解的方法）之后执行一次。 |
| @BeforeAll             | 修饰在方法上，使用该注解的方法在当前整个测试类中所有的测试方法之前执行，每个测试类运行时只会执行一次。 |
| @AfterAll              | 修饰在方法上，与@BeforeAll对应，使用该注解的方法在当前测试类中所有测试方法都执行完毕后执行的，每个测试类运行时只会执行一次。 |
| @Nested                | 使用了这个注解的测试类表示这是一个嵌套的测试类，什么是嵌套测试类呢通俗的说就是在测试类中嵌套一个非静态的测试类（即内部类），并且可以嵌套多层。 |
| @Tag                   | 修饰在类名或方法上，通过 @Tag 对测试类和方法进行打标签。 例如打上product表示只在生产环境执行。 |
| @Disabled              | 修饰在类名或方法上，表示不用执行它。                         |
| @Timeout               | 修饰方法上，表示超过多少时间，方法还没有执行完，就判定测试失败。 |

## 三、测试案例

### 1. @BeforeAll、@AfterAll、@[BeforeEach](https://so.csdn.net/so/search?q=BeforeEach&spm=1001.2101.3001.7020)、@AfterEach

```java
@ExtendWith(MockitoExtension.class)
public class DictTypeControllerTest {

    @BeforeAll
    static void testBeforeAll() {
        System.out.println("BeforeAll");
    }

    @BeforeEach
    void testBeforeEach() {
        System.out.println("BeforeEach");
    }

    @AfterEach
    void testAfterEach() {
        System.out.println("AfterEach");
    }

    @Test
    @DisplayName("方法A")
    void testA() {
        System.out.println("testA");
    }


    @Test
    @DisplayName("方法B")
    void testB() {
        System.out.println("testB");
    }

    @Test
    @DisplayName("方法C")
    @Disabled
    void testC() {
        System.out.println("testC");
    }

    @AfterAll
    static void testAfterAll() {
        System.out.println("AfterAll");
    }
}
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

![在这里插入图片描述](./assets/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LqaIOeRnw==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center.png)

### 2. @ParameterizedTest

```java
@ExtendWith(MockitoExtension.class)
public class ParameterizedAnnoTest {

    @ParameterizedTest
    @MethodSource("getParams")
    void testAdd(int a, int b) {
        System.out.println(a + b);
    }

    private static Stream<Arguments> getParams() {
        return Stream.of(
                Arguments.of(1, 2),
                Arguments.of(3, 2)
        );
    }
}
12345678910111213141516
```

### 3. @RepeatedTest

```java
@ExtendWith(MockitoExtension.class)
public class RepeatedAnnoTest {
    
    @RepeatedTest(3)
    @DisplayName("方法A")
    void testA() {
        System.out.println("testA");
    }
}
123456789
```

结果显示执行了3次

```java
testA
testA
testA
123
```

如果@Test和@RepeatedTest(3)同时都修饰了方法，那么会叠加，总执行4次。

```java
@ExtendWith(MockitoExtension.class)
public class RepeatedAnnoTest {
	
	//这里多了个@Test
    @Test
    @RepeatedTest(3)
    @DisplayName("方法A")
    void testA() {
        System.out.println("testA");
    }
}
1234567891011
```

结果显示执行了4次

```java
testA
testA
testA
testA
```