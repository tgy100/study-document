#### 1.@ReqestMapping注解

##### 1)作用

	RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

##### 2)注解属性

1. ==value==: 指定请求的实际地址，指定的地址可以是URI Template 模式,如果设置了context-path，就相对与context-path，没有就相对于WEB应用的根目录

   ```java
   @RequestMapping(value = {"index","/"},
                       method = RequestMethod.GET)
   ```

2. ==method==: 指定请求的method类型,有如下类型:

   ```java
   public enum RequestMethod {
   	GET, HEAD, POST, PUT, PATCH, DELETE, OPTIONS, TRACE
   }
   ```

3. ==consumes==: 指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;

   ```sql
   @RequestMapping(value = {"index","/"},
                       method = RequestMethod.GET,
                       consumes = "text/html")
       public String index(){
           return  "index";
       }
   ```

   说明: ==上述方法仅处理request 的 Content-Type为“text/html”类型的请求==

4. produces：定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回

   ```java
    @RequestMapping(value = {"index","/"},
                       method = RequestMethod.GET,
                       produces = "text/html")
       public String index(){
           return  "index";
       }
   ```

   说明: 方法仅处理request请求中Accept头中包含了"application/json"的请求，同时暗示了返回的内容类型为text/html;

5. params： 指定request中必须包含某些参数值是，才让该方法处理。

   ```java
   @RequestMapping(value = {"index","/"},
                       method = RequestMethod.GET,
                       produces = "text/html",
                       params = "name=12")  // 也可以只写name
       public String index(){
           
           return  "index";
       }
   ```

   说明: ==仅处理请求中包含了名为“name”==

6. headers： 指定request中必须包含某些指定的header值，才能让该方法处理请求。

   ```java
    @RequestMapping(value = {"index","/"},
                       method = RequestMethod.GET,
                       headers="Referer=http://www.ifeng.com/"
                       )
       public String index(){
           return  "index";
       }
   
   ```

#### 2.@PathVariable

##### 1)作用

	将 URL 中占位符参数绑定到控制器处理方法的入参中，使用方式 {name}对应@PathVariable中的name或者value

##### 2)注解属性

1. value或者name : 与URL中的{name}相对应

2. required:是否必须有
   如果required = false，而你没有传这个参数，那么它会去找这个参数去掉之后的替代url (/student)，如果发现有替代的url，就可以处理这个请求，如果没有找到，就抛出异常不去处理。

   ```java
   //获取@PathVariable对应的值封装到namedValueInfo
   NamedValueInfo namedValueInfo = getNamedValueInfo(parameter);
   MethodParameter nestedParameter = parameter.nestedIfOptional();
   
   Object resolvedName = resolveStringValue(namedValueInfo.name);
   //获取参数
   Object arg = resolveName(resolvedName.toString(), nestedParameter, webRequest);
   if (arg == null) {
       //获取默认值
   	if (namedValueInfo.defaultValue != null) {
   		arg = resolveStringValue(namedValueInfo.defaultValue);
   	}
   	else if (namedValueInfo.required && !nestedParameter.isOptional()) {
           //如果required但是arg为null。抛异常
   		handleMissingValue(namedValueInfo.name, nestedParameter, webRequest);
   	}
   	arg = handleNullValue(namedValueInfo.name, arg, nestedParameter.getNestedParameterType());
   }
   ```

3. 
