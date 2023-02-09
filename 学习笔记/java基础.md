
```java
class Teacher {
    static {
        System.out.println("Teacher 静态代码块");
    }

    {
        System.out.println("Teacher 构造代码块");
    }

    public Teacher() {
        System.out.println("Teacher 构造方法");
    }
}

public class TeacherTest {
    static {
        System.out.println("TeacherTest静态代码块");
    }

    public static void main(String[] args) {
        System.out.println("main方法");

        Teacher t1 = new Teacher();
        Teacher t2 = new Teacher();
    }
}
```

### 执行顺序为
1. TeacherTest静态代码块
2. main方法
3. Teacher 静态代码块
4. Teacher 构造代码块
5. Teacher 构造方法
6. Teacher 构造代码块
7. Teacher 构造方法



> 静态代码块->普通（构造）代码块->构造方法->静态方法->普通方法



数据类型 | 占用字节 | 位数 | 取值范围 | 默认值 |封装类
---|---|---|---|---|---
char	| 2	|16	|0 - 2^16-1	    |空	    |Character
boolean	| 1	|8	|true、false	|false	|Boolean
byte	|1	|8	|-2^7 - 2^7-1	|0	    |Byte
short	|2	|16	|-2^15 - 2^15-1	|0	    |Short
int	    |4	|32	|-2^31 - 2^31-1	|0	    |Integer
long	|8	|64	|-2^63 - 2^63-1	|0	    |Long
float	|4	|32	|-2^31 - 2^31-1	|0	    |Float
double	|8	|64	|-2^63 - 2^63-1	|0	    |Double



> 下面的Java赋值语句哪些是有错误的 （）
> A. int i =1000;
> B. float f = 45.0;
> C. char s = ‘\u0639’
> D. Object o = ‘f’;
> E. String s = "hello,world\0";
> F. Double d = 100;
>
> 正确答案: B C F

>[原文地址](https://learnku.com/articles/60695)
---

