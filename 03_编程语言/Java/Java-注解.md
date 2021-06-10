java注解是在JDK5时引入的新特性。

**什么是自定义注解？**

注解其实就是一种标记，可以在程序代码中的关键节点（类、方法、变量、参数、包）上打上这些标记，然后程序在编译时或运行时可以检测到这些标记从而执行一些特殊操作。因此可以得出自定义注解使用的基本流程：

第一步，定义注解——相当于定义标记；
第二步，配置注解——把标记打在需要用到的程序代码中；
第三步，解析注解——在编译期或运行时检测到标记，并进行特殊操作。

注解在Java中，与类、接口、枚举类似，因此其声明语法基本一致，只是所使用的关键字有所不同，**注解使用`@interface关键字来声明`**。**在底层实现上，所有定义的注解都会自动继承java.lang.annotation.Annotation接口**。

## 基本语法



### 声明注解与元注解

```java
//声明Test注解
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {} 
```

* `@interface`声明为Test注解；
* 使用`@Target`注解传入`ElementType.METHOD`参数来标明@Test只能用于方法上；
* `@Retention(RetentionPolicy.RUNTIME)`则用来表示该注解生存期是运行时；

@Target 用来约束注解可以应用的地方（如方法、类或字段），其中ElementType是枚举类型，其定义如下，也代表可能的取值范围

```java
public enum ElementType {
    /**标明该注解可以用于类、接口（包括注解类型）或enum声明*/
    TYPE,
    
    /** 标明该注解可以用于字段(域)声明，包括enum实例 */
	FIELD,

	/** 标明该注解可以用于方法声明 */
	METHOD,

	/** 标明该注解可以用于参数声明 */
	PARAMETER,

	/** 标明注解可以用于构造函数声明 */
	CONSTRUCTOR,

	/** 标明注解可以用于局部变量声明 */
	LOCAL_VARIABLE,

	/** 标明注解可以用于注解声明(应用于另一个注解上)*/
	ANNOTATION_TYPE,

	/** 标明注解可以用于包声明 */
	PACKAGE,

	/**
 	* 标明注解可以用于类型参数声明（1.8新加入）
 	* @since 1.8
	 */
	TYPE_PARAMETER,

	/**
 	* 类型使用声明（1.8新加入)
 	* @since 1.8
 	*/
	TYPE_USE
}
```
当注解未指定Target值时，则此注解可以用于任何元素之上，多个值使用{}包含并用逗号隔开

```java
@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE})
```



@Retention用来约束注解的生命周期，分别有三个值，源码级别（source），类文件级别（class）或者运行时级别（runtime）

* **SOURCE**：注解将被编译器丢弃（该类型的注解信息只会保留在源码里，源码经过编译后，注解信息会被丢弃，不会保留在编译好的class文件里）

* **CLASS**：注解在class文件中可用，但会被VM丢弃（该类型的注解信息会保留在源码里和class文件里，在执行的时候，不会加载到虚拟机中），请注意，**当注解未定义Retention值时，默认值是CLASS**，如Java内置注解，@Override、@Deprecated、@SuppressWarnning等

* **RUNTIME**：注解信息将在运行期(JVM)也保留，因此可以通过反射机制读取注解的信息（源码、class文件和执行的时候都有注解的信息），如SpringMvc中的@Controller、@Autowired、@RequestMapping等。



### 注解元素及其数据类型

@Test内部没有定义其他元素，所以@Test也称为标记注解（marker annotation）；

在自定义注解中，一般都会包含一些元素以表示某些值，以方便处理器使用.

```java
@Target(ElementType.TYPE)//只能应用于类上
@Retention(RetentionPolicy.RUNTIME)//保存到运行时
public @interface DBTable {
    String name() default "";
}
```

```java
//在类上使用该注解
@DBTable(name = "MEMBER")
public class Member {
    //.......
}
```

关于注解支持的元素数据类型除了上述的String，还支持如下数据类型;

- 所有基本类型（int,float,boolean,byte,double,char,long,short）
- String
- Class
- enum
- Annotation
- 上述类型的数组

> 声明注解元素时可以使用基本类型但不允许使用任何包装类型，同时还应该注意到注解也可以作为元素的类型，也就是嵌套注解。
>
> 注解中定义了名为value的元素，并且在使用该注解时，如果该元素是唯一需要赋值的一个元素，那么此时无需使用key=value的语法，而只需在括号内给出value元素所需的值即可。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface Reference{
    boolean next() default false;
}

public @interface AnnotationElementDemo {
    //枚举类型
    enum Status {FIXED,NORMAL};

    //声明枚举
    Status status() default Status.FIXED;

    //布尔类型
    boolean showSupport() default false;

    //String类型
    String name()default "";

    //class类型
    Class<?> testCase() default Void.class;

    //注解嵌套
    Reference reference() default @Reference(next=true);

    //数组类型
    long[] value();
}
```



### Documented 注解

Documented 注解表明这个注解应该被 javadoc工具记录. 默认情况下,javadoc是不包括注解的. 但如果声明注解时指定了 @Documented,则它会被 javadoc 之类的工具处理, 所以注解类型信息也会被包括在生成的文档中。



### Inherited注解

@Inherited注解，是指定某个自定义注解如果写在了父类的声明部分，那么子类的声明部分也能自动拥有该注解，类似继承。@Inherited注解只对那些@Target被定义为ElementType.TYPE的自定义注解起作用。

```java
@Inherited
@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)

public @interface DocA {}

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface DocB {}

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface CustomA {}

@DocA
public class Student {}

@DocB
public class Teacher {}

public class Jerry extends Student{}

@CustomA
public class DrSmith extends Teacher{}

public static void main(String[] args){
        Jerry jerry = new Jerry();
        Annotation[] annotations = jerry.getClass().getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation.toString());
        }

        DrSmith smith = new DrSmith();
        Annotation[] smithArr = smith.getClass().getAnnotations();
        if (smithArr.length==0){
            System.out.println("annotation is null");
        }else {
            for (Annotation annotation : smithArr) {
                System.out.println(annotation.toString());
            }
        }
}
```

```java
//输出结果
@com.example.DocA()
@com.example.CustomA()
```



### 运行时注解处理器

将注解的生命周期配置成运行时，即在Runntime时保留,才能获取注解中的相关信息。

代码实例：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Information {
    String name() default "jerry";
    int age();
    String[] hobbies();
    String address() default "China";
}

public class Person {
    @Information(name = "Jerry", age = 18, hobbies = {"看电影", "玩游戏"})
    public void showPersonInfo(Person person) {
        System.out.println("primary info: name = " + name + ", age = " + age);
    }
    ...
}

public static void main(String[] args){
        Class<Person> aClass = Person.class;
        try {
            Method method = aClass.getMethod("showPersonInfo", aClass);
            if (!method.isAnnotationPresent(Information.class)){
                System.out.println("Information 注解不存在！");
                return;
            }

            Information information = method.getAnnotation(Information.class);
            Person person = aClass.newInstance();
            person.setName(information.name());
            person.setAge(information.age());
            person.setHobbies(information.hobbies());
            person.setAddress(information.address());
            System.out.println(person.toString());
        } catch (Exception e) {
            e.printStackTrace();
        }
}
```

```java
//输出
com.example.Person{name='Jerry', age=18, hobbies=[看电影, 玩游戏], address=China}
```



### 注解与反射机制

Java所有注解都继承了Annotation接口，也就是说　Java使用Annotation接口代表注解元素，该接口是所有Annotation类型的父接口。同时为了运行时能准确获取到注解的相关信息，Java在java.lang.reflect 反射包下新增了AnnotatedElement接口，它主要用于表示目前正在 VM 中运行的程序中已使用注解的元素，通过该接口提供的方法可以利用反射技术地读取注解的信息，如反射包的Constructor类、Field类、Method类、Package类和Class类都实现了AnnotatedElement接口

| **返回值**             | **方法名称**                                                 | **说明**                                                     |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <A extends Annotation> | getAnnotation(Class<A> annotationClass)                      | 该元素如果存在指定类型的注解，则返回这些注解，否则返回 null。 |
| Annotation[]           | getAnnotations()                                             | 返回此元素上存在的所有注解，**包括从父类继承的**             |
| boolean                | isAnnotationPresent(Class<? extends Annotation> annotationClass) | 如果指定类型的注解存在于此元素上，则返回 true，否则返回 false。 |
| Annotation[]           | getDeclaredAnnotations()                                     | 返回直接存在于此元素上的所有注解，注意，不包括父类的注解，调用者可以随意修改返回的数组；这不会对其他调用者返回的数组产生任何影响，没有则返回长度为0的数组 |

```java
@Inherited
@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)

public @interface DocA {}

@DocA
public class Student {}

@CustomA
public class Jerry extends Student{}

public static void main(String[] args){
        Jerry jerry = new Jerry();
        Class<? extends Jerry> aClass = jerry.getClass();

        //根据指定注解类型获取注解
        DocA docA = aClass.getAnnotation(DocA.class);
        System.out.println(docA.toString());

        //获取元素上所有注解，包含从父类继承
        Annotation[] annotations = aClass.getAnnotations();
        System.out.println(Arrays.toString(annotations));

        //获取元素所有注解，不包含继承；
        Annotation[] declaredAnnotations = aClass.getDeclaredAnnotations();
        System.out.println(Arrays.toString(declaredAnnotations));
		
    	//判断注解是否在该元素上
        boolean isPresentDocA = aClass.isAnnotationPresent(DocA.class);
        System.out.println(isPresentDocA);
        boolean isPresentDocB = aClass.isAnnotationPresent(DocB.class);
        System.out.println(isPresentDocB);
}
```

```java
//输出
@com.example.DocA()
[@com.example.DocA(), @com.example.CustomA()]
[@com.example.CustomA()]
true
false
```



### 小结

**1. 定义注解类型元素时需要注意如下几点：**

* 访问修饰符必须为public，不写默认为public；

* 该元素的类型只能是基本数据类型、String、Class、枚举类型、注解类型（体现了注解的嵌套效果）以及上述类型的一位数组；

* 该元素的名称一般定义为名词，如果注解中只有一个元素，请把名字起为value（后面使用会带来便利操作）；

* ()不是定义方法参数的地方，也不能在括号中定义任何参数，仅仅只是一个特殊的语法；

* default代表默认值，值必须和第2点定义的类型一致；

* 如果没有默认值，代表后续使用注解时必须给该类型元素赋值。



**2. 元注解**

* @Target注解，是专门用来限定某个自定义注解能够被应用在哪些Java元素上面

* @Retention注解，用来修饰自定义注解的生命周期（只有注解信息在运行时保留，我们才能在运行时通过反射操作获取到注解信息）。
* @Documented注解，是被用来指定自定义注解是否能随着被定义的java文件生成到JavaDoc文档当中。
* @Inherited注解，是指定某个自定义注解如果写在了父类的声明部分，那么子类的声明部分也能自动拥有该注解，类似继承。@Inherited注解只对那些@Target被定义为ElementType.TYPE的自定义注解起作用。