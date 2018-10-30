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

   ```java
   //required=false的使用，在@RequestMapping的value加上"/index2"
   @RequestMapping(value = {"/index2/{id}","/index2"},
                       method = RequestMethod.GET)
       public String index2(@PathVariable(value = "id",required = false) String p_id){
   
           System.out.println(p_id);
           return  "index";
    }
   ```


#### 3.@HiddenHttpMethodFilter注解

##### 1）作用

	由于web网页中只有get和post请求，而在实现rest风格的请求时，还有put，delete.该过滤器就是把web页面中带有如下格式的转换成对应的请求方式

```html
<input type="hidden" name="_method" value="put|delete">
```

##### 2）controller中的使用

```java
@PutMapping(value = "login")
public String login(){

	return  "message";
}
```

##### 3)springboot中的配置

在==WebMvcAutoConfiguration==配置文件中

```java
@Bean
@ConditionalOnMissingBean(HiddenHttpMethodFilter.class)
public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
	
	return new OrderedHiddenHttpMethodFilter();
}
```

#### 4.@RequestParam

接收?后面的参数，当不是必须传时，可以如下设置

```java
@GetMapping(value = "/requestParam")
public String requestParam(@RequestParam(value = "age",required = false,defaultValue = "0") int age1){


        System.out.println(age1);
        return "index";
    }
```

#### 5.@RequestParam

##### 获取请求头参数

```java
@GetMapping(value = "/requestParam")
public String requestParam(@RequestHeader(value = "Content-Type",
                                          required = false,
                                          defaultValue = "ss") String contentType){
        System.out.println( contentType);
        return "index";
}
```

#### 6.@CookieValue

##### 获取cooke中的参数

```java
@GetMapping(value = "/requestParam")
public String requestParam(@CookieValue(value = "Webstorm-702797c8",
										required = false,
										defaultValue = "sa")String name){
        System.out.println( name);
        return "index";
}
```

#### 7.controller方法中可以写的参数

```java
@Nullable
private Object resolveArgument(Class<?> paramType, HttpServletRequest request) throws IOException {
	if (HttpSession.class.isAssignableFrom(paramType)) {
		HttpSession session = request.getSession();
		if (session != null && !paramType.isInstance(session)) {
			throw new IllegalStateException(
					"Current session is not of type [" + paramType.getName() + "]: " + session);
		}
		return session;
	}
	else if (pushBuilder != null && pushBuilder.isAssignableFrom(paramType)) {
		return PushBuilderDelegate.resolvePushBuilder(request, paramType);
	}
	else if (InputStream.class.isAssignableFrom(paramType)) {
		InputStream inputStream = request.getInputStream();
		if (inputStream != null && !paramType.isInstance(inputStream)) {
			throw new IllegalStateException(
					"Request input stream is not of type [" + paramType.getName() + "]: " + inputStream);
		}
		return inputStream;
	}
	else if (Reader.class.isAssignableFrom(paramType)) {
		Reader reader = request.getReader();
		if (reader != null && !paramType.isInstance(reader)) {
			throw new IllegalStateException(
					"Request body reader is not of type [" + paramType.getName() + "]: " + reader);
		}
		return reader;
	}
	else if (Principal.class.isAssignableFrom(paramType)) {
		Principal userPrincipal = request.getUserPrincipal();
		if (userPrincipal != null && !paramType.isInstance(userPrincipal)) {
			throw new IllegalStateException(
					"Current user principal is not of type [" + paramType.getName() + "]: " + userPrincipal);
		}
		return userPrincipal;
	}
	else if (HttpMethod.class == paramType) {
		return HttpMethod.resolve(request.getMethod());
	}
	else if (Locale.class == paramType) {
		return RequestContextUtils.getLocale(request);
	}
	else if (TimeZone.class == paramType) {
		TimeZone timeZone = RequestContextUtils.getTimeZone(request);
		return (timeZone != null ? timeZone : TimeZone.getDefault());
	}
	else if (ZoneId.class == paramType) {
		TimeZone timeZone = RequestContextUtils.getTimeZone(request);
		return (timeZone != null ? timeZone.toZoneId() : ZoneId.systemDefault());
	}

	// Should never happen...
	throw new UnsupportedOperationException("Unknown parameter type: " + paramType.getName());
}
```

```java
@Override
public Object resolveArgument(MethodParameter parameter, @Nullable 												  ModelAndViewContainer mavContainer,
                              NativeWebRequest webRequest, 
                              @Nullable WebDataBinderFactory binderFactory) throws Exception {

	Class<?> paramType = parameter.getParameterType();

	// WebRequest / NativeWebRequest / ServletWebRequest
	if (WebRequest.class.isAssignableFrom(paramType)) {
		if (!paramType.isInstance(webRequest)) {
			throw new IllegalStateException(
					"Current request is not of type [" + paramType.getName() + "]: " + webRequest);
		}
		return webRequest;
	}

	// ServletRequest / HttpServletRequest / MultipartRequest / MultipartHttpServletRequest
	if (ServletRequest.class.isAssignableFrom(paramType) || MultipartRequest.class.isAssignableFrom(paramType)) {
		return resolveNativeRequest(webRequest, paramType);
	}

	// HttpServletRequest required for all further argument types
	return resolveArgument(paramType, resolveNativeRequest(webRequest, HttpServletRequest.class));
}
```



通过源码，可以放：HttpSession,InputStream,Reader,Principal,HttpMethod,Locale



