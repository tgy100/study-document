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

#### 8.ModelAndView

##### 1)源码处理

DispatcherServlet 类的render(mv, request, response);

```java
protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
	// Determine locale for request and apply it to the response.
	Locale locale =(this.localeResolver != null ? this.localeResolver.resolveLocale(request) : request.getLocale());
	response.setLocale(locale);
    
	View view;
	String viewName = mv.getViewName();
	if (viewName != null) {
		
		view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
		if (view == null) {
			throw new ServletException("Could not resolve view with name '" + mv.getViewName() +
					"' in servlet with name '" + getServletName() + "'");
		}
	}
	else {
	
		view = mv.getView();
		if (view == null) {
			throw new ServletException("ModelAndView [" + mv + "] neither contains a view name nor a " +
					"View object in servlet with name '" + getServletName() + "'");
		}
	}
	try {
		if (mv.getStatus() != null) {
			response.setStatus(mv.getStatus().value());
		}
        //解释视图，把逻辑视图与物理视图关联
		view.render(mv.getModelInternal(), request, response);
	}
	catch (Exception ex) {
		
		throw ex;
	}
}
```

在AbstractView方法中把ModelView中的数据放到了request域中

```java
protected void exposeModelAsRequestAttributes(Map<String, Object> model,
			HttpServletRequest request) throws Exception {

	model.forEach((modelName, modelValue) -> {
		if (modelValue != null) {
			request.setAttribute(modelName, modelValue);
		}
		else {
			request.removeAttribute(modelName);
		}
	});
}
```

#### 9.@SessionAttributes

##### 1)作用

	按照key取出ModelMap中的值，该key在ModelMap和注解@SessionAttributes的names字段中都存在。把取出来的值放到session域中。

##### 2)注意

1. @SessionAttributes只能在方法中使用

##### 3)源码分析

RequestMappingHandlerAdapter 类的getModelAndView方法调用

```java
modelFactory.updateModel(webRequest, mavContainer);
```

ModelFactory 类的的updateModel中把modelMap中存在的key应用的值设置到session域中

```java
public void updateModel(NativeWebRequest request, ModelAndViewContainer container) throws Exception {
	ModelMap defaultModel = container.getDefaultModel();
	if (container.getSessionStatus().isComplete()){
		this.sessionAttributesHandler.cleanupAttributes(request);
	}
	else {
		//设置到session域中
		this.sessionAttributesHandler.storeAttributes(request, defaultModel);
	}
	if (!container.isRequestHandled() && container.getModel() == defaultModel) {
		updateBindingResult(request, defaultModel);
	}
}
```

#### 10.@ModelAttribute

1. 作用

   提前在mavContainer容器中放入数据库中的数据，使得在页面上没有提交的数据在这里填充到对应的model中，在接下来的update中不至于把数据库中的字段修改为null

2. 使用

   - 在指定的controller类中写一个方法，方法的上面加上@ModelAttribute,参数就是需要什么就加上，Spring会自动填充

     ```sql
     @ModelAttribute()
     public void before(@RequestParam(value = "id",required = false) String id ,ModelMap modelMap){
     
             if (id != null){
                 
                 //从数据库中查找对应id的Person数据
                 Person person = new Person();
                 person.setAge(23);
                 //把从数据库中查到的数据放到modelMap中,这个的key要与下面@ModelAttribute修饰的参数的value值一致，如果@ModelAttribute没写，则直接是参数类型名小写
                 modelMap.put("person",person);
             }
         }
     ```

   - 在需要接收model的地方

     ```sql
     @PostMapping(value = "/modelAttributeTest")
     public String modelAttributeTest(@ModelAttribute(value = "person") Person person){
     
         System.out.println(person);
         return  "message";
     }
     ```

     ==注意:==

     ==如果没有加@ModelAttribute，默认参数为类名小写==

3. 源码分析

   1. @ModelAttribute注解标记的方法执行位置

      ```java
      RequestMappingHandlerAdapter invokeHandlerMethod
      modelFactory.initModel(webRequest, mavContainer, invocableMethod);
      ```

      ```java
      public void initModel(NativeWebRequest request, ModelAndViewContainer container, HandlerMethod handlerMethod)
      			throws Exception {
      
      	Map<String, ?> sessionAttributes = this.sessionAttributesHandler.retrieveAttributes(request);
      	container.mergeAttributes(sessionAttributes);
          //调用有@ModelAttribute注解标记的方法，把数据包装到container中
      	invokeModelAttributeMethods(request, container);
      
          //findSessionAttributeArguments 查找@ModelAttribute标记的参数的value值,同时在@SessionAttributes的value中要存在对应的值
      	for (String name : findSessionAttributeArguments(handlerMethod)) {
              //如果container里面没有，则去session域中查找
      		if (!container.containsAttribute(name)) {
      			Object value = this.sessionAttributesHandler.retrieveAttribute(request, name);
                  //session域中没查到，就抛异常
      		if (value == null) {
      				throw new HttpSessionRequiredException("Expected session attribute '" + name + "'", name);
      			}
      			container.addAttribute(name, value);
      		}
      	}
      }
      ```

   2. 有@ModelAttribute参数的处理

      ```java
      //在 ModelAttributeMethodProcessor 类的resolveArgument方法中调用如下方法获取属性名对应的key
      String name = ModelFactory.getNameForParameter(parameter);
      ```

      ```java
      public static String getNameForParameter(MethodParameter parameter) {
          //获取参数的ModelAttribute注解
      	ModelAttribute ann = parameter.getParameterAnnotation(ModelAttribute.class);
          //如果加了ModelAttribute，则取注解里面的value对应的值
      	String name = (ann != null ? ann.value() : null);
          //如果没有，则调用Conventions.getVariableNameForParameter(parameter)，获取参数类型名小写
      	return (StringUtils.hasText(name) ? name : Conventions.getVariableNameForParameter(parameter));
      }
      ```

4. @SessionAttributes引发的异常

   There was an unexpected error (type=Internal Server Error, status=500).
   ==Expected session attribute 'person'==

   在源码解析的第一点的==initModel==方法注释中


#### 11.自定义视图解析器

1. 定义一个继承View接口的类MyView

   ```java
   public class MyView implements View {
       @Override
       public String getContentType() {
           return "application/json";
       }
   
       @Override
       public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
   
            response.getWriter().println("name=tgy");
       }
   }
   ```

2. 在springmvc配置文件中加入到spring容器中

   ```java
   @Bean("index")
   public View myView(){
   
       return  new MyView();
   }
   ```

   注意，该bean的name就是视图名，即controller方法返回的字符串名，如下:

   ```java
   @GetMapping(value = "/modelAndView")
   public String modelAndView( ModelMap modelMap){
   
   	return  "index";
   }
   ```


#### 12.重定向

1. 使用

   ```java
   @GetMapping(value = "/redirectTest")
   public String redirectTest(){
   		
      	
       //return  "redirect:/modelAndView";或者
       return  "forward:/modelAndView";
   }
   ```

    

2. 源码

   ```
   UrlBasedViewResolver类的createView方法里面
   ```

   ```java
   @Override
   protected View createView(String viewName, Locale locale) throws Exception {
   	// If this resolver is not supposed to handle the given view,
   	// return null to pass on to the next resolver in the chain.
   	if (!canHandle(viewName, locale)) {
   		return null;
   	}
   
   	// Check for special "redirect:" prefix.
   	if (viewName.startsWith(REDIRECT_URL_PREFIX)) {
   		String redirectUrl = viewName.substring(REDIRECT_URL_PREFIX.length());
   		RedirectView view = new RedirectView(redirectUrl,
   				isRedirectContextRelative(), isRedirectHttp10Compatible());
   		String[] hosts = getRedirectHosts();
   		if (hosts != null) {
   			view.setHosts(hosts);
   		}
   		return applyLifecycleMethods(REDIRECT_URL_PREFIX, view);
   	}
   
   	// Check for special "forward:" prefix.
   	if (viewName.startsWith(FORWARD_URL_PREFIX)) {
   		String forwardUrl = viewName.substring(FORWARD_URL_PREFIX.length());
   		return new InternalResourceView(forwardUrl);
   	}
   
   	// Else fall back to superclass implementation: calling loadView.
   	return super.createView(viewName, locale);
   }
   ```

#### 13.参数的赋值与验证

1. 源码

   ```
   ModelAttributeMethodProcessor 类的 resolveArgument 方法
   ```

   ```java
   @Override
   @Nullable
   public final Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
   		NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
   
   	String name = ModelFactory.getNameForParameter(parameter);
   	ModelAttribute ann = parameter.getParameterAnnotation(ModelAttribute.class);
   	if (ann != null) {
   		mavContainer.setBinding(name, ann.binding());
   	}
   
   	Object attribute = null;
   	BindingResult bindingResult = null;
   
   	if (mavContainer.containsAttribute(name)) {
   		attribute = mavContainer.getModel().get(name);
   	}
   	else {
   		// Create attribute instance
   		try {
   			attribute = createAttribute(name, parameter, binderFactory, webRequest);
   		}
   		catch (BindException ex) {
   			if (isBindExceptionRequired(parameter)) {
   				// No BindingResult parameter -> fail with BindException
   				throw ex;
   			}
   			// Otherwise, expose null/empty value and associated BindingResult
   			if (parameter.getParameterType() == Optional.class) {
   				attribute = Optional.empty();
   			}
   			bindingResult = ex.getBindingResult();
   		}
   	}
   
   	if (bindingResult == null) {
   		// Bean property binding and validation;
   		// skipped in case of binding failure on construction.
   		WebDataBinder binder = binderFactory.createBinder(webRequest, attribute, name);
   		if (binder.getTarget() != null) {
   			if (!mavContainer.isBindingDisabled(name)) {
                	//绑定参数   
   				bindRequestParameters(binder, webRequest);
   			}
               //验证参数
   			validateIfApplicable(binder, parameter);
   			if (binder.getBindingResult().hasErrors() && isBindExceptionRequired(binder, parameter)) {
   				throw new BindException(binder.getBindingResult());
   			}
   		}
   		// Value type adaptation, also covering java.util.Optional
   		if (!parameter.getParameterType().isInstance(attribute)) {
   			attribute = binder.convertIfNecessary(binder.getTarget(), parameter.getParameterType(), parameter);
   		}
   		bindingResult = binder.getBindingResult();
   	}
   
   	// Add resolved attribute and BindingResult at the end of the model
   	Map<String, Object> bindingResultModel = bindingResult.getModel();
   	mavContainer.removeAttributes(bindingResultModel);
   	mavContainer.addAllAttributes(bindingResultModel);
   
   	return attribute;
   }
   ```

#### 14.自定义类型转换器(Converter)

1. 实现Converter，或者GenericConverter，或者Formatter接口

   ```java
   @Component
   public class MyConverter implements Converter<String,Person> {
   
       @Override
       public Person convert(String s) {
   
           String ps[] = s.split("-");
   
           if (ps.length == 3){
   
               Person person = new Person(ps[0],ps[1],Integer.valueOf(ps[2]));
   
               return person;
           }
   
           return null;
       }
   }
   
   ```

2. 在springboot中的配置

   由于springboot的WebMvcAutoConfiguration类中有如下实现

   ```java
   @Override
   public void addFormatters(FormatterRegistry registry) {
   	for (Converter<?, ?> converter : getBeansOfType(Converter.class)) {
   		registry.addConverter(converter);
   	}
   	for (GenericConverter converter : getBeansOfType(GenericConverter.class)) {
   		registry.addConverter(converter);
   	}
   	for (Formatter<?> formatter : getBeansOfType(Formatter.class)) {
   		registry.addFormatter(formatter);
   	}
   }
   ```

   即只需要把实现接口的类放到==spring容器==中即可完成类型转换器的安装

3. ==注意点==

   - 如果自定义的类型转换器的转换类型在容器中存在，则自定义的类型无法加到容器中

     ```java
     private GenericConversionService.ConvertersForPair getMatchableConverters(ConvertiblePair convertiblePair) {
         GenericConversionService.ConvertersForPair convertersForPair = (GenericConversionService.ConvertersForPair)this.converters.get(convertiblePair);
         //如果不存在对应的类型转换器，才将该类型转换器加入
         if (convertersForPair == null) {
             convertersForPair = new GenericConversionService.ConvertersForPair();
             this.converters.put(convertiblePair, convertersForPair);
         }
     
         return convertersForPair;
     }
     ```

     如果非要替换容器中的类型转换器，则可以在自定义的配置文件(SpringMVCConfig)的addFormatters方法中先删除对应的转换器，然后加上自定义的类型转换器。

     ```java
     @Override
     public void addFormatters(FormatterRegistry registry) {
     
         registry.removeConvertible(String.class,Integer.class);
         //自定义类型转换
         registry.addConverter(new MyConverter());
     }
     ```


#### 15.@initBinder注解

1. 作用

   用于设置WebDataBinder，在参数赋值和验证中定义自己的规则

2. 使用

   在对应的controller类中写一个方法，该方法不能有返回值，同时参数通常是WebDataBinder类型

   ```java
   @InitBinder
   public void coustomDataBinder(WebDataBinder webDataBinder){
   
   	//设置不接受的字段
       webDataBinder.setDisallowedFields("username");
   }
   ```

#### 16.数据校验

1. 校验步骤

   - 在controller方法中的参数加上@Valid注解，同时在该参数的属性上加上如下的注解

     ```java
     public class Person {
     
         @Length(min = 6,message = "长度不够")
         private String username;
         @Email(message = "不是email")
         private String password;
         @Min(value = 18,message = "年龄不够")
         private Integer age;
     
         @Past(message = "日期在当前时间之后")
         @DateTimeFormat(pattern = "yyyy-MM-dd")
         private Date birthday;
     
         public Date getBirthday() {
             return birthday;
         }
     
         public void setBirthday(Date birthday) {
             this.birthday = birthday;
         }
     
         public String getUsername() {
             return username;
         }
     
         public void setUsername(String username) {
             this.username = username;
         }
     
         public Person() {
     
             System.out.println("无参构造器");
         }
     
         public Person(String username, @Email String password, Integer age) {
             this.username = username;
             this.password = password;
             this.age = age;
         }
     
         public String getPassword() {
             return password;
         }
     
         public void setPassword(String password) {
             this.password = password;
         }
     
         public Integer getAge() {
             return age;
         }
     
         public void setAge(Integer age) {
             this.age = age;
         }
     
         public Person(Integer age) {
             this.age = age;
             System.out.println("age参构造器");
         }
     }
     ```

     在该方法中加上==BindingResult==类，在其中获取验证失败的列和对应的信息

     ```java
     public String modelAttributeTest(@Valid Person person, BindingResult result){
     
             if (result.hasErrors()){
     
                 result.getFieldErrors().stream().forEach(x->{
     
                     System.out.println(x.getDefaultMessage());
                     System.out.println(x.getField());
                 });
     
               		
                  result.getAllErrors()
                  .stream()
                  .map(ObjectError::getDefaultMessage)
                  .forEach(System.out::println);
               		
             }
     
             System.out.println(person);
             return  "message";
         }
     ```

2. 校验类型

   - 空检查

     - @Null      

        验证对象是否为null

     - @NotNull

       验证对象是否不为null, 无法查检长度为0的字符串

     - @NotBlank

        检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.

     - @NotEmpty

        检查约束元素是否为NULL或者是EMPTY.

   - Booelan检查

     - @AssertTrue 

       验证 Boolean 对象是否为 true  

     - @AssertFalse

         验证 Boolean 对象是否为 false

   - 长度检查

     - @Size(min=, max=)

       验证对象（Array,Collection,Map,String）长度是否在给定的范围之内  

     - @Length(min=, max=)

       验证字符串长度是否在该范围内

   - 日期检查

     - @Past

       验证 Date 和 Calendar 对象是否在当前时间之前  

     - @Future    

       验证 Date 和 Calendar 对象是否在当前时间之后  

     - @Pattern

       验证 String 对象是否符合正则表达式的规则

     - @DateTimeFormat

       格式化日期    

   - 数值检查

     建议使用在Stirng,Integer类型，不建议使用在int类型上，因为表单值为“”时无法转换为int，但可以转换为Stirng为"",Integer为null

     - @Min

       验证 Number 和 String 对象是否大等于指定的值  

     - @Max

       验证 Number 和 String 对象是否小等于指定的值  

     - @DecimalMax

       被标注的值必须不大于约束中指定的最大值. 这个约束的参数是一个通过BigDecimal定义的最大值的字符串表示.小数存在精度

     - @DecimalMin 

       被标注的值必须不小于约束中指定的最小值. 这个约束的参数是一个通过BigDecimal定义的最小值的字符串表示.小数存在精度

     - @Digits

       验证 Number 和 String 的构成是否合法  

     - @Digits(integer=,fraction=) 

       验证字符串是否是符合指定格式的数字，interger指定整数精度，fraction指定小数精度。

     - @Range(min=, max=) 

       检查数字是否介于min和max之间.

       ```java
       @Range(min=10000,max=50000,message="不在指定的范围内")
       private BigDecimal wage;
       ```

     - @NumberFormat

       格式化数字

   - 其他常用检查

     - @Valid 

       递归的对关联对象进行校验, 如果关联对象是个集合或者数组,那么对其中的元素进行递归校验,如果是一个map,则对其中的值部分进行校验.(是否进行递归验证)

     - @CreditCardNumber

       信用卡验证

     - @Email

       验证是否是邮件地址，如果为null,不进行验证，算通过验证。

     - @ScriptAssert(lang= ,script=, alias=)

       如果需要校验的业务逻辑比较复杂，简单的@NotBlank，@Min注解已经无法满足需求了，这时可以使用@ScriptAssert来指定进行校验的方法，通过方法来进行复杂业务逻辑的校验，然后返回true或false来表明是否校验成功。例如下面的例子：

       ```java
       //通过script 属性指定进行校验的方法，传递校验的参数，
       @ScriptAssert(lang = "javascript", 
                    script = "com.learn.validate.domain.Student.checkParams(_this.name,_this.age,_this.classes)", 
                   message = "验证失败")
       public class Student {
           private String name;
           private int age;
           private String classess; //注意进行校验的方法要写成静态方法，否则会出现   //TypeError: xxx is not a function 的错误
       
           public static boolean checkParams(String name, int age, String classes) {
               if (name != null && age > 8 & classes != null) {
                   return true;
               } else {
                   return false;
               }
           }
       }
       ```

     - @URL(protocol=,host=, port=,regexp=, flags=)

       url验证

3. 自定义校验类型

   (1)编写annotation

   ```java
   @Target( {ElementType.METHOD, ElementType.FIELD, ElementType.ANNOTATION_TYPE })
   @Retention(RetentionPolicy.RUNTIME)
   @Constraint(validatedBy = UsernameValidate.class)
   public @interface UsernameCheck {
   
       String message() default "用户名错误";
       Class<?>[] groups() default {};
   
       Class<? extends Payload>[] payload() default {};
   }
   ```

   (2)编写校验类(实现ConstraintValidator接口)

   ```java
   public class UsernameValidate implements ConstraintValidator<UsernameCheck,String> {
       
       //保存指定的annotation
       private UsernameCheck usernameCheck;
       @Override
       public boolean isValid(String s, ConstraintValidatorContext constraintValidatorContext) {
   
           if (s.length() > 6 && s.matches("[0-9A-Za-z_]*")){
   
               return true;
           }
   
           return false;
       }
   
       @Override
       public void initialize(UsernameCheck constraintAnnotation) {
           //接收指定的annotation
           usernameCheck = constraintAnnotation;
       }
   }
   ```

   (3)在对应的bean中使用

   ```java
   @UsernameCheck(message = "用户名太简单")
   private String username;
   ```
