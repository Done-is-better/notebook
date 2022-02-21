 #### 3.1 Stream流

```java
//匿名内部类形式
public static void main(String[] args){
        List<Author> list = getAuthor();
        list.stream()
                .distinct()
                .filter(new Predicate<Author>() {
                    @Override
                    public boolean test(Author author) {
                        return Integer.valueOf(author.getAge()) < 31;
                    }
                })
                .forEach(new Consumer<Author>() {
                    @Override
                    public void accept(Author author) {
                        System.out.println(author.getName());
                    }
                });

    }
```

```java
//lambda
 public static void main(String[] args){
        List<Author> list = getAuthor();
        list.stream()
                .distinct()
                .filter(author -> Integer.valueOf(author.getAge()) < 31)
                .forEach(author ->  System.out.println(author.getName() + author.getAge()));

    }
```

```java
//Author类注解
@Data
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode //相当于重写了所有属性的equals方法
```

#### 3.4 常用创建流的操作

> **单列集合**
>
> **<a>Collection</a>接口提供<a>stream()</a>方法**
>
> ```java
> List<Author> authors = ...
> Stream<Author> stream = authors.stream();
> ```
>
> **数组**
>
> <a>Arrays.stream(数组)</a>或<a>Stream.of()</a> 
>
> ```java
> Integer[] arr = {1,2,3,4};
> Stream<Integer> stream = Arrays.stream(arr);
> Stream<Integer> stream = Arrays.of(arr);
> ```
>
> **双列集合：转换成单列集合后再创建**
>
> ```java
> Map<String,Integer> map = new HashMap<>();
> map.put("xxx",19);
> map.put("xxxx",18);
> map.put("123",17);
> Stream<Map.Entry<String,Integer>> stream = map.entrySet().stream();
> ```
>
> 

#### 3.5 中间操作

> **filter**

> **map**
>
> 可以对流中的元素进行计算或转换

```java
List<Author> list = ...
list.stream()
      .map(new Function<Author, String>() { //map(Function<? super Author,? extends R>)
             @Override
              public String apply(Author author) {
                 return author.getName();
               }
      })
      .forEach(System.out::println);
```

```java
//lambda
List<Author> list = ...
list.stream()
       .map(author -> author.getName())
       .forEach(System.out::println);
```



> **distinct**
>
> 去除流中重复元素
>
> **<a>distinct 方法依赖Object的 equals方法来判断是否是相同对象的。所以要重写equals方法</a>** 

> **sorted**
>
> 可以对流中的元素排序
>
> sorted提供两个方法：
>
> <a>sorted()</a> :流中元素的类要实现<a>Comparable</a>接口
>
> <a>sorted(Comparable<? super String>)</a> 



```java
//匿名内部类
 list.stream()
                .distinct()
                .sorted(new Comparator<Author>() {
                    @Override
                    public int compare(Author o1, Author o2) {
                        return Integer.valueOf(o2.getAge()) -Integer.parseInt(o1.getAge());
                    }
                })
                .forEach(author -> System.out.println(author.getName() + author.getAge()));
```

```java
//lambda
  list.stream()
                .distinct()
                .sorted((o1,o2) -> Integer.valueOf(o2.getAge()) -Integer.parseInt(o1.getAge()))
                .forEach(author -> System.out.println(author.getName() + author.getAge()));
```



> **limit**
>
> 可以设置流的最大长度，超出的部分将被抛弃

```java
list.stream()
                .distinct()
                .sorted((o1,o2) -> Integer.parseInt(o2.getAge()) -Integer.parseInt(o1.getAge()))
                .limit(2)
                .forEach(author -> System.out.println(author.getName() + author.getAge()));
```

> **skip**
>
> 跳过流中前n个元素，返回剩下的元素

```java
list.stream()
                .distinct()
                .sorted((o1,o2) -> Integer.parseInt(o2.getAge()) -Integer.parseInt(o1.getAge()))
                .skip(2)
                .forEach(author -> System.out.println(author.getName() + author.getAge()));
```

> **flatMap**
>
> flatMap可以把一个对象转换成多个对象作为流中的元素

```java
list.stream()
                .flatMap(new Function<Author, Stream<Book>>() {
                    @Override
                    public Stream<Book> apply(Author author) {
                        return author.getBooks().stream();
                    }
                })
                .distinct()
                .forEach(new Consumer<Book>() {
                    @Override
                    public void accept(Book book) {
                        System.out.println(book.getBookName());
                    }
                });
```

```java
//lambda
list.stream()
                .flatMap( author -> author.getBooks().stream())
                .distinct()
                .forEach(book -> System.out.println(book.getBookName()));
```

```java
//例二
list.stream()
                .flatMap(author -> author.getBooks().stream())
                .distinct()
    //String[] split(String regex)
                .flatMap(book -> Arrays.stream(book.getBookName().split(" ")))
                .forEach(System.out::println);
```





#### 3.4.3 终结操作

> **forEach**
>
> 对流中的元素进行遍历操作，通过传入的参数自定对遍历到的元素进行具体的操作。

> **count** 返回值为<a>long</a> 类型
>
> 用来获取当前流中元素的个数

```java
long n = list.stream()
                .flatMap(author -> author.getBooks().stream())
                .distinct()
                .map(Book::getBookName)
                .distinct()
                .count();
        System.out.println(n);
```

> **max & min**
>
> 可以用来处理流中的最值

```java
Optional<Integer> res = list.stream()
                .flatMap(author -> author.getBooks().stream())
                .distinct()
                .map(Book::getPrice)
                .max((price1, price2) -> price1 - price2);
        System.out.println(res.get());
```

> **collect**
>
> 把当前流转变成一个集合

```java
List<String> names = list.stream()
                .distinct()
                .sorted()
                .map(Author::getName)
                .collect(Collectors.toList());
```

```java
Set<String> bookSet = list.stream()
                .flatMap(author -> author.getBooks().stream())
                .map(Book::getBookName)
                .collect(Collectors.toSet());
```





```java
Map<String,List<Book>> bookMap =  list.stream()
                .distinct()
                .collect(Collectors.toMap(new Function<Author, String>() {
                    @Override
                    public String apply(Author author) {
                        return author.getName();
                    }
                }, new Function<Author, List<Book>>() {
                    @Override
                    public List<Book> apply(Author author) {
                        return author.getBooks();
                    }
                }));
```

```java
//lambda
Map<String,List<Book>> bookMap =  list.stream()
                .distinct()
                .collect(Collectors.toMap( author -> author.getName() , author -> author.getBooks()));
```



##### 查找和匹配

> **anyMatch**  Predicate接口
>
> 判断是否有任意符合匹配条件的元素，返回值类型为boolean

```java
boolean res = list.stream()
                .anyMatch(author -> Integer.parseInt(author.getAge()) > 88);
```

> **allMatch**
>
> 判断是否都符合条件

```java
boolean res = list.stream()
                .allMatch(author -> Integer.parseInt(author.getAge()) > 20);
```

> **noneMatch**
>
> 都不匹配

```java
boolean res = list.stream()
    .noneMatch(author -> Integer.parseInt(author.getAge()) > 20);
```

> **findAny**
>
> 找到任意一个元素，不保证是第一个元素

```java
Optional<Author> author = list.stream()
                .findAny();
```

> **findFirst**
>
> 找到第一个元素

```java
Optional<Author> author = list.stream()
                .filter(author1 -> Integer.parseInt(author1.getAge()) > 20)
                .findFirst();
        author.ifPresent(System.out::println);
```

##### reduce

> 对流中的数据按照指定的计算方法算出结果
>
> reduce 的作用是把stream流中的元素组合起来，按照指定的计算方式一次拿流中的元素和在初始值的基础上进行计算
>
> 内部实现方式：
>
> ```java
> T result = identity;//identity是通过方法参数传入的初始值
> for(T element : this.stream){
>     result = accumulator.apply(rsult,element)//accumulator的apply具体进行什么计算也是通过方法参数来确定的。
> }
> return result;
> ```
>
> 

```java
int res =  list.stream()
                .map(author -> Integer.parseInt(author.getAge()))
                .reduce(0, new BinaryOperator<Integer>() {
                    @Override
                    public Integer apply(Integer result, Integer element) {
                        return result + element;
                    }
                });
```

```java
//lambda
int res =  list.stream()
                .map(author -> Integer.parseInt(author.getAge()))
                .reduce(0,(result,element) -> result + element);
```

```java
int res =  list.stream()
                .map(author -> Integer.parseInt(author.getAge()))
                .reduce(0, Integer::sum);
```

列二

```java
int res = list.stream()
                .map(author -> Integer.parseInt(author.getAge()))
                .distinct()
                .reduce(Integer.MIN_VALUE,(age1,age2) -> Math.max(age1,age2));
```

```java
 int res = list.stream()
                .map(author -> Integer.parseInt(author.getAge()))
                .distinct()
                .reduce(Integer.MIN_VALUE, Math::max);
```

> **reduce 一个参数的内部实现**
>
> ```java
> boolean foundAny = false;
> T result = null;
> for(T element : this stream){
>     if(!foundAny){
>         foundAny = true;
>         result = element;
>     }else{
>         result = accumulator.apply(result,element);
>     }
> }
> return foundAny?Optional.of(result):Optional.empty();
> ```
>
> 





#### 3.5注意

> - 惰性求值，如果没有终结操作，中间操作不会执行
> - 流是一次性的，经过一次终结操作后，这个流就不能再被使用了
> - 不会影响元数据

