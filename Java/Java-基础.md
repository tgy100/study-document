### 一.Lambda表达式

#### 1)Lambda简介

	 Java8引入了一个新的操作符"==->==",该操作符称为尖头操作符或Lambda操作符。箭头操作符把Lambda表达式分成两部分:

1. Lambda左侧:表达式的参数列表
2. Lambda右侧:表达式中所需执行的功能，即Lambda体

#### 2)Lambda基础语法

1. 无参数，无返回值

   ```java
   () -> lambda体
   ```

   ```java
   @Test
   public void test01(){
   
       Runnable runnable = ()-> System.out.println("sada");
       runnable.run();
   }
   ```

   tips：==若Lambd体中只有一条语句时，return和大括号可以省略==

2. 有一个参数，无返回值

   ```java
   (T)-> lambda体
   ```

   ```java
   @Test
   public void test02(){
   
       Consumer<String> consumer = (name)-> System.out.println(name);
       consumer.accept("张三");
   }
   ```

   tips:

   - ==如参数只有一个，则参数的小括号可以省略==

     ```
     Consumer<String> consumer = name -> System.out.println(name);
     ```

3. 有两个以上参数，有返回值，并且lambda体中有多条语句

   ```
   (x,y) -> {
       
      方法体
   }
   ```

   ```java
   @Test
   public void test03(){
   
       List<Integer> list = Arrays.asList(3,4,1,2,6,9,8);
       Comparator<Integer> comparator = (t1,t2) ->{
   
           return  Integer.compare(t1,t2);
       };
       list.sort(comparator);
       System.out.println(list);
   }
   ```

   tips：==lambda表达式的参数列表的数据类型可以省略不写，因为JVM编译器可以通过上下文推断出数据类型。==

#### 3) Lambda表达式需要 函数式接口 的支持

1. 函数式接口的定义

   接口中只有==一个抽象方法==的接口，称为函数式接口。

   可以使用==@FunctionalInterface==修饰，表明该接口是函数式接口

2. 例子

   - 定义

     ```java
     @FunctionalInterface
     public interface MyLamdbaInterface<T> {
     
         public T compare(T t1, T t2);
     
     }
     ```

   - 使用

     ```java
     @Test
     public void test04(){
     
         MyLamdbaInterface<Integer> myLamdbaInterface = (t1,t2)-> {
     
             return  t1 - t2;
         };
     
         useTest04(myLamdbaInterface);
     }
     
     public void useTest04(MyLamdbaInterface myLamdbaInterface){
     
         System.out.println(myLamdbaInterface.compare(10, 20));;
     }
     ```

#### 4)内置的函数式接口

1. 消费型接口(Consumer)

   ```java
   @FunctionalInterface
   public interface Consumer<T> {
   
       /**
        * Performs this operation on the given argument.
        *
        * @param t the input argument
        */
       void accept(T t);
   
   }
   ```

2. 供给型接口(Supplier)

   ```java
   @FunctionalInterface
   public interface Supplier<T> {
   
       /**
        * Gets a result.
        *
        * @return a result
        */
       T get();
   }
   ```

3. 函数型接口(Function)

   ```java
   @FunctionalInterface
   public interface Function<T, R> {
   
       /**
        * Applies this function to the given argument.
        *
        * @param t the function argument
        * @return the function result
        */
       R apply(T t);
   
       /**
        * Returns a function that always returns its input argument.
        *
        * @param <T> the type of the input and output objects to the function
        * @return a function that always returns its input argument
        */
       static <T> Function<T, T> identity() {
           return t -> t;
       }
   }
   
   ```

4. 断言型接口(Predicate)

   ```java
   @FunctionalInterface
   public interface Predicate<T> {
   
       /**
        * Evaluates this predicate on the given argument.
        *
        * @param t the input argument
        * @return {@code true} if the input argument matches the predicate,
        * otherwise {@code false}
        */
       boolean test(T t);
   
       /**
        * Returns a predicate that tests if two arguments are equal according
        * to {@link Objects#equals(Object, Object)}.
        *
        * @param <T> the type of arguments to the predicate
        * @param targetRef the object reference with which to compare for equality,
        *               which may be {@code null}
        * @return a predicate that tests if two arguments are equal according
        * to {@link Objects#equals(Object, Object)}
        */
       static <T> Predicate<T> isEqual(Object targetRef) {
           return (null == targetRef)
                   ? Objects::isNull
                   : object -> targetRef.equals(object);
       }
   }
   ```

5. 其他函数式接口

   ![](./image/lambda/function_interface_other.png)