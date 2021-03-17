## 什么是反射

JVM为每个加载的`class`创建了对应的`Class`实例，并在实例中保存了该`class`的所有信息，包括类名、包名、父类、实现的接口、所有方法、字段等，因此，如果获取了某个`Class`实例，我们就可以通过这个`Class`实例获取到该实例对应的`class`的所有信息。

这种通过`Class`实例获取`class`信息的方法称为反射（Reflection）。



## 一. 使用反射获取对象，并调用方法的步骤

- 获取类的 Class 对象实例

```
Class clz = Class.forName("com.zhenai.api.Apple");
```

- 根据 Class 对象实例获取 Constructor 对象

```
Constructor appleConstructor = clz.getConstructor();
```

- 使用 Constructor 对象的 newInstance 方法获取反射类对象

```
Object appleObj = appleConstructor.newInstance();
```

- 获取方法的 Method 对象

```
Method setPriceMethod = clz.getMethod("setPrice", int.class);
```

- 利用 invoke 方法调用方法

```
setPriceMethod.invoke(appleObj, 14);
```



## 二、反射常用API

### 1.获取反射中class对象的三种方法

**第一种，使用 Class.forName 静态方法。**当你知道该类的全路径名时，你可以使用该方法获取 Class 类对象。

```
Class clz = Class.forName("java.lang.String");
```

**第二种，使用 .class 方法。**(这种方法只适合在编译前就知道操作的 Class)

```
Class clz = String.class;
```

**第三种，使用类对象的 getClass() 方法。**

```
String str = new String("Hello");
Class clz = str.getClass();
```



### 2.通过反射创建对象的两种方法

> 通过反射创建类对象主要有两种方式，通过 Constructor 对象创建类对象可以选择特定构造方法，而通过 Class 对象则只能使用默认的无参数构造方法。
>
> 1. 通过 Class 对象的 newInstance() 方法；
>
> 2. 通过 Constructor 对象的 newInstance() 方法；

第一种：通过 Class 对象的 newInstance() 方法。

```
Class clz = Apple.class;
Apple apple = (Apple)clz.newInstance();
```

第二种：通过 Constructor 对象的 newInstance() 方法

```
Class clz = Apple.class;
Constructor constructor = clz.getConstructor();
Apple apple = (Apple)constructor.newInstance();
```



### 3. 访问字段

#### 获取属性

我们通过 Class 对象的 getFields() 方法可以获取 Class 类的属性，但无法获取私有属性。

使用 Class 对象的 getDeclaredFields() 方法则可以获取包括私有属性在内的所有属性。

```java
Class<Person> personClass = Person.class;
Field[] fields = personClass.getDeclaredFields();

for (Field field : fields) {
    System.out.println(field.getName()+","+Modifier.toString(field.getModifiers()));
}
```

输出

```
TAG, public static final
age, private
name, private
```



#### 获取字段值

对于一个`Person`实例，我们可以先拿到`name`字段对应的`Field`，再获取这个实例的`name`字段的值：

```java
public static void main(String[] args) {
        Person person = new Person(18);
        Class<? extends Person> aClass = person.getClass();
        try {
            Field age = aClass.getDeclaredField("age");
            age.setAccessible(true);
            Object o = age.get(person);
            System.out.println(o);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

```java
//输出
18
```

如果 accessible 标志被设置为true，那么反射对象在使用的时候，不会去检查Java语言权限控制（private之类的）,即不管这个字段是不是public，一律允许访问。

#### 设置字段值

设置字段值是通过`Field.set(Object, Object)`实现的，其中第一个`Object`参数是指定的实例，第二个`Object`参数是待修改的值。

```java
public static void main(String[] args) {
        Person person = new Person(18);
        Class<? extends Person> aClass = person.getClass();
        try {
            Field age = aClass.getDeclaredField("age");
            age.setAccessible(true);
            Object o = age.get(person);
            System.out.println(o);
            age.set(person,22);
            System.out.println(person);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

输出

```
18
com.example.Person{age=22, name='null'}
```



#### 小结

* Java的反射API提供的`Field`类封装了字段的所有信息：

* 通过`Class`实例的方法可以获取`Field`实例：`getField()`，`getFields()`，`getDeclaredField()`，`getDeclaredFields()`；

* 通过Field实例可以获取字段信息：`getName()`，`getType()`，`getModifiers()`；

* 通过Field实例可以读取或设置某个对象的字段，如果存在访问限制，要首先调用`setAccessible(true)`来访问非`public`字段。

* 通过反射读写字段是一种非常规方法，它会破坏对象的封装。



### 4. 调用方法

`Class`类提供了以下几个方法来获取`Method`：

- `Method getMethod(name, Class...)`：获取某个`public`的`Method`（包括父类）
- `Method getDeclaredMethod(name, Class...)`：获取当前类的某个`Method`（不包括父类）
- `Method[] getMethods()`：获取所有`public`的`Method`（包括父类）
- `Method[] getDeclaredMethods()`：获取当前类的所有`Method`（不包括父类）



一个`Method`对象包含一个方法的所有信息：

- `getName()`：返回方法名称，例如：`"getScore"`；
- `getReturnType()`：返回方法返回值类型，也是一个Class实例，例如：`String.class`；
- `getParameterTypes()`：返回方法的参数类型，是一个Class数组，例如：`{String.class, int.class}`；
- `getModifiers()`：返回方法的修饰符，它是一个`int`，不同的bit表示不同的含义。

```java
public static void main(String[] args) throws Exception{
        Class<?> aClass = Person.class;
        Person person = (Person) aClass.getConstructor().newInstance();
        Method method = aClass.getMethod("setName",String.class);
        method.invoke(person,"Jerry");

        Method getName = aClass.getMethod("getName");
        String invoke = (String) getName.invoke(person);
        System.out.println(invoke);
    }
    
// 输出
// Jerry
```

对`Method`实例调用`invoke`就相当于调用该方法，`invoke`的第一个参数是对象实例，即在哪个实例上调用该方法，后面的可变参数要与方法参数一致，否则将报错。

#### **调用静态方法**

如果获取到的Method表示一个静态方法，调用静态方法时，由于无需指定实例对象，所以`invoke`方法传入的第一个参数永远为`null`。我们以`Integer.parseInt(String)`为例：

```java
public static void main(String[] args) throws Exception {
        // 获取Integer.parseInt(String)方法，参数为String:
        Method m = Integer.class.getMethod("parseInt", String.class);
        // 调用该静态方法并获取结果:
        Integer n = (Integer) m.invoke(null, "12345");
        // 打印调用结果:
        System.out.println(n);
    }
```

```java
//输出
//12345
```



#### **调用非public方法**

和Field类似，对于非public方法，我们虽然可以通过`Class.getDeclaredMethod()`获取该方法实例，但直接对其调用将得到一个`IllegalAccessException`。为了调用非public方法，我们通过`Method.setAccessible(true)`允许其调用：

```java
public static void main(String[] args) throws Exception {
        Person p = new Person();
        Method m = p.getClass().getDeclaredMethod("setName", String.class);
        m.setAccessible(true);
        m.invoke(p, "Bob");
        System.out.println(p.name);
    }
```

```java
//输出
//Bob
```



#### 小结

Java的反射API提供的Method对象封装了方法的所有信息：

通过`Class`实例的方法可以获取`Method`实例：`getMethod()`，`getMethods()`，`getDeclaredMethod()`，`getDeclaredMethods()`；

通过`Method`实例可以获取方法信息：`getName()`，`getReturnType()`，`getParameterTypes()`，`getModifiers()`；

通过`Method`实例可以调用某个对象的方法：`Object invoke(Object instance, Object... parameters)`；

通过设置`setAccessible(true)`来访问非`public`方法；

通过反射调用方法时，仍然遵循多态原则。



### 5. 调用构造方法

通过反射来创建新的实例，可以调用Class提供的newInstance()方法：

```
Person p = Person.class.newInstance();
```

调用Class.newInstance()的局限是，它只能调用该类的public无参数构造方法。如果构造方法带有参数，或者不是public，就无法直接通过Class.newInstance()来调用。

为了调用任意的构造方法，Java的反射API提供了Constructor对象，它包含一个构造方法的所有信息，可以创建一个实例。Constructor对象和Method非常类似，不同之处仅在于它是一个构造方法，并且，调用结果总是返回实例.

```java
public static void main(String[] args) throws Exception{
        Person person = new Person();
        Class<? extends Person> aClass = person.getClass();
        Constructor<?>[] constructors = aClass.getDeclaredConstructors();

        for (Constructor<?> constructor : constructors) {
            //getModifiers 返回修饰符类型
            System.out.print(constructor+", 参数：");
            //getParameterTypes 返回方法参数类型
            Class<?>[] types = constructor.getParameterTypes();

            for (Class<?> type : types) {
                System.out.print(type.getName() + " ");
            }

            System.out.println();
        }
    }
```

```java
//输出
private com.example.Person(java.lang.String), 参数：java.lang.String 
public com.example.Person(int,java.lang.String), 参数：int java.lang.String 
public com.example.Person(int), 参数：int 
public com.example.Person(), 参数：
```

通过Class实例获取Constructor的方法如下：

- `getConstructor(Class...)`：获取某个`public`的`Constructor`；
- `getDeclaredConstructor(Class...)`：获取某个`Constructor`；
- `getConstructors()`：获取所有`public`的`Constructor`；
- `getDeclaredConstructors()`：获取所有`Constructor`。

注意`Constructor`总是当前类定义的构造方法，和父类无关，因此不存在多态的问题。

调用非`public`的`Constructor`时，必须首先通过`setAccessible(true)`设置允许访问。`setAccessible(true)`可能会失败。

#### **通过构造方法创建实例**

```java
public static void main(String[] args){
        try {
            Person person = new Person();
            Class<? extends Person> aClass = person.getClass();
            //两个参数
            Class<?>[] p = {int.class, String.class};
            Constructor<? extends Person> constructor = aClass.getDeclaredConstructor(p);
            Person instance = constructor.newInstance(22, "张三");
            System.out.println(instance.toString());
        } catch (Exception e) {
            e.printStackTrace();
        }
 }
```



#### 小结

`Constructor`对象封装了构造方法的所有信息；

通过`Class`实例的方法可以获取`Constructor`实例：`getConstructor()`，`getConstructors()`，`getDeclaredConstructor()`，`getDeclaredConstructors()`；

通过`Constructor`实例可以创建一个实例对象：`newInstance(Object... parameters)`； 通过设置`setAccessible(true)`来访问非`public`构造方法。



### 6. 获取继承关系

通过`Class`对象可以获取继承关系：

- `Class getSuperclass()`：获取父类类型；
- `Class[] getInterfaces()`：获取当前类实现的所有接口。

通过`Class`对象的`isAssignableFrom()`方法可以判断一个向上转型是否可以实现。