### 1. 文件系统

linux下没有专门的系统，可以认为，除了home目录，其他都是系统目录。

### 2. 常用指令

#### pwd  
查看当前目录

#### cd ~ 
切换目录
​	“..” 代表当前目录的上一级
​	"." 代表当前目录
​	"~" 代表用户主目录

#### touch
若文件不存在，创建一个空文件；
若文件存在，则更新文件的修改时间为当前时间；



#### cp
cp 源	目标
cp -rf  a1  a2  //目录拷贝，r表示拷贝子目录，f表示强制；
cp  main.cpp   main1.cpp   // 实现重命名；



#### mv  移动

mv  hello  ~/picture/    //将hello文件移动到picture文件夹下
mv  -i  hello  test/      // -i 指令用于提示重名时的警告



#### tar 文件归档

tar -cvf  xxx.tar  file1 file2 file3

c: create 创建
v: verbose  显示过程信息
f: file  文件
x: extract  提取

tar -xvf xxx.tar   // 拆包
tar -xvf  xxx.tar  -C /opt/del  

tar -zvcf xxx.tar.gz  file1 file2 file3
tar -xzvf xxx.tar.gz



#### grep查找指令

grep -rn "hello world" ./

./ 表示路径为当前目录
-r 表示递归查找
-n 显示行号
-i 忽略大小写



#### find 指令

find /home -name "*.txt"

> 通配符

> “*” 匹配任意长度的字符串
>“?” 匹配一个字符
> “[]” 匹配所有出现在方括号内的字符
> "-" 可以指定一个字符集范围  ls text[1-3]



#### Linux主要目录

/etc  系统软件的启动和配置文件
/lib  c编译器的库
/opt  可选的应用软件包
/usr  非系统的程序和命令



#### 建立目录 mkdir

mkdir ~/temp/job   //如果temp目录不存在，添加-p选项



#### 输入输出重定向

ls > ~/ls_out       // “>”表示直接覆盖
ls >> ~/ls_out     // “>>”表示追加



#### 输入重定向

cat < days



#### 管道 |

ls | grep sys   // 将一条命令的输出连接到另一条命令的输入；  

