| 返回值       | 方法                                                         | 方法解释                                                     |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| T            | `getAnnotation(Class<A> annotationClass)`                    | 返回元素上存在的、指定类型的注解，如果该类型的注解不存在，则返回空 |
| Annotation[] | `getAnnotations()`                                           | 返回元素上存在的所有注解                                     |
| T            | `getDeclaredAnnotation(Class<A> annotationClass)`            | 返回元素上存在的、指定类型的注解，==忽略继承注解==，如果该类型的注解不存在，则返回空 |
| Annotation[] | `getDeclaredAnnotations()`                                   | 返回直接存在于元素上的所有注解==忽略继承注解==               |
| boolean      | `isAnnotationPresent(Class<? extends Annotation> annotationClass)` | 判断元素上是否存在该注解                                     |

> 总结：getDeclaredAnnotations将不包含继承的注解，getAnnotations则包含



其他的java.lang.Class中有关**getDeclaredxxx**和**getxxx**的方法以getDeclaredMethods和getMethods为例说明

**getDeclaredMethod(s)**：返回自身类的**所有公用（public）方法包括私有(private)方法**,这些对象反映此 Class 对象表示的类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，==但不包括继承的方法==。

> 返回数组中的元素没有排序，也没有任何特定的顺序。如果该类或接口不声明任何方法，或者此 Class 对象表示一个基本类型、一个数组类或 void，则此方法返回一个长度为 0 的数组。
>
> 类初始化方法 <Constructor> 不包含在返回数组中。如果该类声明带有相同参数类型的多个公共成员方法，则它们都包含在返回的数组中。



**getMethod(s)**：返回某个类的**所有公用（public）方法包括其继承类的公用方法，当然也包括它所实现接口的方法**。这些对象反映此 Class 对象所表示的类或接口（包括那些由该类或接口声明的以及从超类和超接口继承的那些的类或接口）的公共 member 方法。

> 数组类返回从 Object 类继承的所有（公共）member 方法。
>
> 返回数组中的元素没有排序，也没有任何特定的顺序。如果此 Class 对象表示没有公共成员方法的类或接口，或者表示一个基本类型或 void，则此方法返回长度为 0 的数组。
> 总之：getDeclaredMethods的关键词是：自身，所有方法，不继承而getMethods的关键词是public 继承

**getDeclaredField（s）和getField（s）同上。**

getDeclaredAnnotation（s）：返回直接存在于此元素上的所有注释。与此接口中的其他方法不同，该方法将忽略继承的注释。（如果没有注释直接存在于此元素上，则返回长度为零的一个数组。）该方法的调用者可以随意修改返回的数组；这不会对其他调用者返回的数组产生任何影响。

getAnnotation（s）：返回此元素上存在的所有注释。（如果此元素没有注释，则返回长度为零的数组。）该方法的调用者可以随意修改返回的数组；这不会对其他调用者返回的数组产生任何影响。

==getDeclaredAnnotations得到的是当前成员所有的注释，不包括继承的。而getAnnotations得到的是包括继承的所有注释。==

关键在于继承的问题上，getDeclaredAnnotations和getAnnotations是否相同，就在于父类的注解是否可继承，这可以用sun.reflect.annotation.AnnotationType antype3=AnnotationType.getInstance(Class.forName(annotationtype_class(example:"javax.ejb.Stateful")).isInherited())来判定，如果为true，说明可以被继承则存在与getAnnotations之中而不在getDeclaredAnnotations之中，否则，也不存在与getannnotations中，因为不能被继承。