---
title: 为什么我们需要Lambda表达式
tags: java8实战
categories: java基础
---

# 为什么我们需要Lambda表达式

## 为了灵活的应对的需求的变化

### 农场主苹果过滤案例

Apple 实体类：

```java
@Data
class Apple {
    Apple(String color, String weight) {
      	this.color = color;
      	this.weight = weight;
    }
  
  	private String color;
  	private String weight;
}

List<Apple> apples = Array.asList(
  new Apple("green", 100), 
  new Apple("red", 110), 
  new Apple("green", 111)
);
```

需求1：找出绿色的苹果

```java
List<Apple> findGreenApples(List<Apple> apples);

List<Apple> greenApples = new ArrayList()<>;
for(Apple apple:apples) {
   if(apple.getColor.equal("green")) {
      greenApples.add(apple);
   }
}

System.out.println(greenApples);
```

需求2：找出大于100克的苹果

```java
List<Apple> findGt100Apples(List<Apple> apples);

List<Apple> gt100Apples = new ArrayList()<>;
for(Apple apple:apples) {
   if(apple.getWeight > 100) {
      gt100Apples.add(apple);
   }
}

System.out.println(gt100Apples);
```

需求3：找出绿色的大于100克的苹果

```java
List<Apple> findGreenAndGt100Apples(List<Apple> apples);

List<Apple> greenAndGt100Apples = new ArrayList()<>;
for(Apple apple:apples) {
   if(apple.getColor.equal("green") && apple.getWeight > 100) {
      greenAndGt100Apples.add(apple);
   }
}

System.out.println(greenAndGt100Apples);
```

需求4：找到红色的苹果

```java
List<Apple> findRedApples(List<Apple> apples);

List<Apple> redApples = new ArrayList()<>;
for(Apple apple:apples) {
   if(apple.getColor.equal("red")) {
      redApples.add(apple);
   }
}

System.out.println(redApples);
```

### 缺点

1. 重复的代码太多；
2. 不方便维护与扩展；
3. 站在人的角度出发，阅读性也不高；

### 改进

#### 本质：让方法的参数具有行为能力

1. 策略模式
2. 匿名内部类
3. Lambda表达式

```java
// 策略模式
interface filterApple {
     boolean fa(apple);
}

class greenFilterApple imp filterApple{
  	public boolean fa(apple){
       if(apple.getColor.equal("green")) {
         return true;
       } else {
         return false;
       }
    }
}

class gt100FilterApple imp filterApple{
  	public boolean fa(apple){
       if(apple.getWeight > 100) {
         return true;
       } else {
         return false;
       }
    }
}

class appFuns(){
   public static List<Apple> findApples(List<Apple> apples, interface filterApple) {
       List<Apple> result = new ArrayList()<>;
       for(Apple apple:apples) {
          if(filterApple.fa(apple)) {
            result.add(apple);
          }
       }
   }
}

List<Apple> greenApples = appFuns.findApples(apples, new gt100FilterApple());
List<Apple> gt100Apples = appFuns.findApples(apples, new gt100FilterApple());
System.out.println(greeApples);
System.out.println(gt100Apples);

// 匿名类、内部类

// lambda表达式
List<Apple> greenApples = apples.strems()
  .filter(apple -> apple.getColor().equal("green"))
  .conxxxx.toList();

List<Apple> gt100Apples = apples.strems()
  .filter(apple -> apple.getWeight > 100)
  .conxxxx.toList();
```



### 为什么要用lambda

1. 结构清晰，思维语意化；
2. 适应改变（自己就是方法的本体）；
3. 节省内存空间（java7 和 java8 内存分配实战，内部类和lambda字节码更节省内存空间）；



# lambda实战

## lambda的语法

```java
Comparator<Apple> byColor = new Comparator<Apple>() {
   @Override
   public int compar(Apple o1, Apple o2) {
      return o1.getColor().comparTo(o2.getColor);
   }
}

// lambda
Comparator<Apple> byColor2 = (o1, o2) -> o1.getColor().compareTo(o2.getColor());

parameter list     arrow       lambda body
(o1, o2)            ->         o1.getColor().compareTo(o2.getColor());

// Function
Function<String, Integer> stringFunction = s->s.length();
Predicate<Apple> p = (Apple apple)->a.getColor().equals("green");

()->42
public interface Test{
   public int fun();
}

(int x, int y) -> {
   System.out.println(x);
	 System.out.println(y);
}

public interface Test{
   public void fun(int x, int y);
}
```

## 函数式接口

### 什么是函数式接口

定义：一个接口，只有一个需要去实现的方法（默认实现方法除外）。可以使用@FunctionalInterface注解标示，在编译期间，会执行是否是函数式接口检查，不使用 @FunctionalInterface 注解但是符合定义的，也是函数式接口。

### 常见一些函数式接口

```java
Predicate boolean test(T t); // 判断
Consumer accept(T t); // 无返回值
Function<T, R> R apply(T t); //  输入输出
Supplier<T> T get(); // 
```

## 内置的函数式接口

### Predicate

```java
List<Apple> filter(List<Apple> apples, Predicate<Apple> predicate) {
   List<Apple> result = new ArrayList<>();
   for(Apple a:apples) {
      if(predicate.test(a)) 
         result.add(a);
   }
   return result;
};

// LongPredicate
List<Apple> filterByW(List<Apple> apples, LongPrediceate<Apple> predicate) {
   List<Apple> result = new ArrayList<>();
   for(Apple a:apples) {
      if(predicate.test(a.getW())) 
         result.add(a);
   }
   return result;
}

filterByW(list, w -> w > 100);

// BiPredicate 两个参数的输入
List<Apple> filterByWandC(List<Apple> apples, BiPredicate<String, Long> predicate) {
   List<Apple> result = new ArrayList<>();
   for(Apple a : apples) {
      if(predicate.test(a.getC, a.getW())) {
         result.add(a);
      }
   }
}

List<Apple> filterByWandC(list, (s, w) -> s.equals("green") && w>100);
```

### consumer

```java
// Consumer
List<Apple> simpleConsumer(List<Apple> apples, Consumer<Apple> consumer) {
  for(Apple a : apples) {
      consumer.accept(a);
  }
}
simpleConsumer(apples, a -> System.out.prinln(a));

// BiConsumer
List<Apple> simpleBiConsumer(String c, List<Apple> apples, BiConsumer<Apple, String> consumer) {
  for(Apple a : apples) {
      consumer.accept(a);
  }
}
simpleBiConsumer("xxx", apples, (a, s) -> System.out.prinln(s + a.getC()));
```

### function

```java
// Function
Apple testFun(Apple apple, Function<Apple, String> fun) {
  return apple.apply(apple);
}

String result3 = testFun(new Apple("yellow", 100), (a)-> a.toString())
System.out.prinln(result3);

// BiFunction
// 自定义Function
```



### supplier 

```java
// Supplier
Supplier<String> s = String::new; // method inference
System.out.println(s.get().getClass());
```



## 方法推导 Method references

```java
Consumer<String> consumer = (s) -> System.out.println(s);
useConsumer(consumer, "Hello Alex");

private static <T> void useConsumer(Consumer<T> consumer, T t) {
		consumer.accept(t);
  	consumer.accept(t);
}

// 方法推导，更加简单的实现
useConsumer(s -> System.out.println(s), "Hello Alex");
useConsumer(System.out::println, "Hello Alex");


// 什么情况下可以进行方法推导
Function<String, Interger> f = Integer::parseInt;
Integer result = f.apply("123");
System.out.println(result);

String string = new String("Hello");
Function<Integer, Character> f3 = String::charAt;
Character c2 = f3.apply(4);
System.out.println(c2);


```



## 类型推导 Type inference

## 练习：多线程改写成lambda

```java
Runnable r1 = ()->System.out.prinln("Hello");

Runnable r2 = new Runnable() {
   @Override
   public void run() {
      System.out.println("Hello");
   }
}

process(r1);
process(r2);
process(()->System.out.prinln("Hello"));

private static void process(Runnable r) {
   r.run();
}
```

## 

## 补充：匿名函数，变量要是不可变的，final


