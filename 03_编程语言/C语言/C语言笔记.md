# 一. C语言常用命令

###  1. 进入cygwin目录

 cd /cygwin

### 2. 编译操作

gcc test.c   //默认生成a.out

gcc -c test.c  //会生成test.o文件

gcc -o test test.c  //生成名字是test的可执行文件

### 3. 结构体

结构体用“点”表示；

指针用"箭头"表示；

### 4. 头文件

```c
//windows.h包含的头文件
system("Pause");
Sleep(1000);
system("cls");

//stdlib.h包含的头文件
rand();
srand();
```



#####  #define和typedef

* define是在预编译时候处理，只是做简单的字符串替换
* typedef在编译阶段处理，不是简单的替换处理，而是采用同定义的方法来处理，有利于程序的移植和通用； 



##### 1. 使用String代替char


```c
typedef char String[20];
String str = "Hello World!";
printf("%s",str);
```


```c
typedef char *String;
char *p = "Hello World";
String p = "Hello World";
printf("%s",p);
```


##### 2. 结构体定义的几种类型

①.先定义结构体，然后在定义结构体变量
```c
struct stu{ /*定义学生结构体类型*/
    char name[20]; //学生姓名
    char sex; //性别
    long num; //学号
    float score[3]; //三科考试成绩
}
struct stu student1,student2; //定义结构体类型变量
struct stu student3,student4;
//用此结构体类型，可以定义更多的该结构体类型变量。
```


②.定义结构体类型同时定义结构体类型变量
```c
struct data{
    int day;
    int month;
    int year;
} time1,time2;
//也可以在定义如下变量:
struct data time3,time4;
//用此结构体类型，同样可以定义更多的该结构体类型变量。
```


③.直接定义结构体类型变量
``` 
struct{
    char name[20]; //学生姓名
    char sex; //性别
    long num; //学号
    float score[3]; //三科考试成绩
} person1,person2; //定义该结构体类型变量
//该定义方法由于无法记录该结构体类型，所以除直接定义外，不能再定义该结构体类型变量。
```


④.在C中定义一个结构体类型要用typedef
```c
typedef struct Student{
    int a;
}Stu;
//于是在声明变量的时候就可： Stu stu1;
/*
如果没有typedef就必须用struct Student stu1;来声明
这里的Stu, 实际上就是 struct Student的别名
另外这里也可以不写Student（于是也不能struct Student stu1;了）

```