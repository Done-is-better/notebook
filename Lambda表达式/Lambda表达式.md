#### 2.Lambda表达式

> Lambda是JDK8中的一个语法糖。可以对某些匿名内部类的写法进行简化。它是函数式编程思想的一个重要体现。

> **核心原则**
>
> 可推导可省略
>
> **基本格式**
>
> (参数列表) ->{代码}

例：

```java
//使用匿名内部类创建线程
new Thread(new Runnable(){
    public void run(){
        System.out.println("hello");
    }
}).start();
```

```java
//lambda写法：
new Thread(() -> {
    System.out.println("hello")
}).start();
```

例二：

```java
public static void main(String[] args){
    printNum((int value) ->{return value%2 == 0});
}

public static void printNum(IntPredicate predicate){
    int[] arr = {1,2,3,4,5,6,7,8,9,10};
    for(int i:arr){
        if(predicate.test(i)){
            System.our.println(i);
        }
    }
}
```

例三：

```java
Integer result = typeConver((String s)->{return Integer.valueOf(s)});
System.out.println(result);

public static<R> R typeConver(Function<String,R> function){
    String str = "1234";
    R result = function.apply(str);
    return result;
}
```

例四：

```java
public static void main(String[] args){
    foreachArr((int value) ->{
        System.out.println(value);
    })
}

public static void foreachArr(IntConsumer consumer){
    int arr[] = {1,2,3,4,5,6,7,8,9,10};
    for(int i:arr){
        consumer.accept(i);
    }
}
```

#### 省略规则

> - 参数类型可以省略
> - 方法体只有一句代码时大括号、return和唯一一句代码的分号可以省略
> - 方法只有一个参数时小括号可以省略

