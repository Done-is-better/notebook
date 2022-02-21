 #### 4.1 概述

> JDK8 中引入了<a>Optional</a> ,使用Optional可以避免空指针异常。

#### 4.2 使用

> Optional可以把具体数据封装到Optional对象内部，使用Optional中封装好的方法操作封装进去的数据可以避免空指针异常。
>
> ```java
> //Optional.ofNullable()源码
> public static <T> Optional<T> ofNummable(T value){
>     return value == null? empty():of(value);
> }
> ```
>
> 

```java
//使用Optional的静态方法<a>ofNullable</a>把数据封装成一个Optional对象，无论传入的参数是否为null都不会出问题。
Author author = getAuthor();
Optional<Author> authorOptional = Optional.ofNullable(author);
```

MyBatis从3.5 版本也支持Optional了，可以直接把dao 方法的返回值类型定义成Optional类型，MyBatis会子集把数据封装成Optional对象返回。

```java
Optional<Author> authorOptional = Optional.ofNullable(Author.getInstance());
//使用ifPresent()判断，存在，执行 ifPresent(Consumer<? super T>)
authorOptional.ifPresent(author -> System.out.println(author.getName()));
```



> 如果<a>**确定一个对象不是空**</a> ,则可以使用<a>**Optional的静态方法of**</a> 来把数据封装成Optional对象。

```java
assert Student.getInstance() != null;
Optional<Student> opt = Optional.of(Student.getInstance());
```

> 如果一个方法的返回值类型是Optional类型。经判断发现某次计算得到的返回值为null，这时要把null封装成Optional对象返回。可以使用<a>Optional的静态方法empty来封装</a> 
>
> ```java
> Optional.empty()
> ```
>
> 

##### 4.2.2 安全消费值

> 获取一个Optional对象后可以使用<a>ifPresent</a> 方法消费其中的值。
>
> 这个方法会判断Optional对象内部封装的值是否为空，不为空时才会执行具体代码。

```java
Optional<Student> stu = Optional.ofNullable(Student.getInstance());
stu.ifPresent(stud -> System.out.println(stud.getName()));
```

##### 4.2.3 获取值

> <a>public T get()</a> 
>
> 如果Optional存在值，返回，否则抛出<a>NoSuchElementException</a> 异常。

##### 4.2.4 安全的获取值

> <a>**orElseGet**</a> 
>
> 获取数据并且设置数据为空时的默认值。如果数据不为空就能获取到该数据。如果为空，就<a>根据传入的参数来创建对象作为默认值返回</a> 

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
Author author = authorOptional.orElseGet(() -> new Author());
```

```java
Optional<Student> studentOptional = Optional.ofNullable(Student.getInstance());
Student stud = studentOptional.orElseGet(Student::new);
```

> <a>**orElseThrow**</a> 
>
> 获取数据，如果数据不为空就能获取到该数据。如果为空<a>根据传入的参数创建异常抛出</a> 

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
try{
    Author author = authorOptional.orElseeThrow((Supplier<Throwable>)() -> new RuntimeException("author is null"));
    System.out.println(author.getName());
}catch(Throwable e){
    e.printStackTrace();
}
```

##### 4.2.5 过滤

> 使用filter 方法对数据过滤。<a>如果原本由数据，但是不符合条件，也会变成一个无数据的Optional</a> 

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
authorOptional.filter(author -> author.getAge()>100)
    .ifPresent(author -> System.out.println(author.getName()));
```

##### 4.2.6 判断

> 使用<a>isPresent</a> 可以判断是否存在数据。如果为空返回false.

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
if(authorOptional.isPresent()){
    ...
}
```

##### 数据转换

```java
Optional<Student> studentOptional = Optional.ofNullable(new Student("chandler",12));
studentOptional.map(Student::getName)
                .ifPresent(System.out::println);
```

