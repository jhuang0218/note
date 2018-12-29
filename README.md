### 3-2
- @RequestMapping
- QueryCondition
- @PageableDefault - page size sort(age,asc)
- jsonPath
- MockMvc
### 3-3
- @PathVariable - 正則表達式 /user/{id:\\d+}
- @JsonView
### 3-4
- @RequestBody
- @Valid
- BindingResult
- @NotBlank
### 3-5
- @Past
- ObjectError -> FieldError
- MyConstraint
```java
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = MyConstraintValidator.class)
public @interface MyConstraint {
    
}
```
### 3-6
- BasicErrorController
- 自訂義錯誤頁面 /resources/error
- @ControllerAdvice
- @ExceptionHandler
- @ResponseStatus
### 3-7
- Filter
```java
@Component
public class TimeFilter implements Filter{}
```
```java
@Configuration
public class WebConfig{
    @Bean
    public FilterRegistrationBean timeFilter() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean() 
        TimeFilter timeFilter = new TimeFilter()
        filterRegistrationBean.setFilter(timeFilter);
        return filterRegistrationBean;
    }
}
```
- Interceptor
```java
@Compoonent
public class TimeInterceptor implements HandlerInterceptor{
    
}
```
```java
@Configuration
public class WebConfig extends WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry interceptorRegistry){
        interceptorRegistry.addInterceptor(xxx);
    }        
}
```
### 3-8
- Aspect
```java
@Aspect
@Component
public class TimeAspect {
    
    @Around("execution(* com.imooc.web.controller.UserController.*(..))")
    public Object handlerControllerMethod(ProceedingJoinPoint pjp){
         
    }
}
```
- Filter -> Interceptor -> ControllerAdvice -> Aspect -> Controller
### 3-9
- UploadFile
```java
@RestController
@RequestMapping("/file")
 public class FileController {
    
    @PostMapping
    public FileInfo upload(MultipartFile file){
        String folder = "xxx";
        File localFile = new File(folder, "xxx");
        file.transerTo(localFile);
        
    }
 }
```
- Download
```java
@RestController
@RequestMapping("/file")
 public class FileController {
    
    @GetMapping("/{id}")
    public void download(@PathVariable String id, HttpServletRequest request, HttpServletReponse reponse){
        try(InputStream inputStream = new FileInputStream(new File(folder, id + ".txt"));
            OutputStream outputStream = response.getOutputStream();){
            
            reponse.setContentType("application/x-download");
            response.addHeader("Content-Disposition", "attachment;filename=test.txt");
            IoUtils.copy(inputStream, outputStream);
            outputStream.flush();
        }
    }
 }
```
### 3-10
- Callable
- DeferredResult
- CallableProcessingInterceptor
### 3-11
- Swagger
  - @ApiOperation(value = "xxx")
  - @ApiModelProperty(value = "xxx")
  - @ApiParam("xxx" )
### 3-12 
- WireMock
```java
public class MockServer {
    public static void main(String[] args){
      WireMock.configureFor()
    }
}
```
### 4-2
- WebSecurityConfigurerAdapter
```java
public class BrowserSecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
        .and()
        .authorizeRequests()
        .anyRequest()
        .authenticated();
    }
}
```
- UsernamePasswordAuthenticationFilter -> BasicAuthenticationFilter -> ExceptionTranslation -> FilterSecurityInterceptor
### 4-3
- UserDetailsService
- UserDetails -> User
- PasswordEncoder -> BCryptPasswordEncoder
```java
public class BrowserSecurityConfig extends WebSecurityConfigurerAdapter {
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```
### 4-4
- 自定義頁面
```java
public class BrowserSecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
        .loginPage("/imooc-signIn.html")
        .loginProcessingUrl("/authentication/form")
        .and()
        .authorizeRequests()
        .antMatchers("/imooc-signIn.html").permitAll()
        .anyRequest()
        .authenticated();
    }
}
```
- RequestCache、SavedRequest
```java
@RestController 
public class BrowserSecurityController{
    private RequestCache requestCache = new HttpSessionRequestCache();
    private RedirectStrategy redirectStrategy = new DefaultRedirectStrategy();
    
    @GetMapping("/authentication/require")
    @ResponseStatus(code = HttpStatus.UNAUTHORIZED)
    public SimpleResponse requireAuthentication(HttpServletRequest request, HttpServletResponse response){
        SavedRequest savedRequest = requestCache.getRequest(request, response);
        if(savedRequest != null){
            String targetUrl = savedRequest.getRedirectUrl();
            if(StringUtils.endsWithIgnoreCase(targetUrl, ".html")){
                redirectStrategy.sendRedirect(request, response);
            }
        }
        return new SimpleResponse("訪問的服務需要身份認證");
    } 
}
```
### 4-5
- AuthenticationSuccessHandler
  - SavedRequestAwareAuthenticationSuccessHandler
- AuthenticationFailureHandler
  - SimpleUrlAuthenticationFailureHandler
### 4-6
- SecurityContextHolder
- SecurityContextPersistenceFilter
- @AuthenticationPrincipal
### 4-7
- SessionStrategy -> HttpSessionSessionStrategy
- OncePerRequestFilter
### 4-9
- 配置
  - PersistentTokenRepository -> JdbcTokenRepositoryImpl
### 4-11
- AbstractAuthenticationProcessingFilter -> SmsAuthenticationFilter
- AbstractAuthenticationToken -> SmsAuthenticationToken
- AuthenticationProvider -> SmsAuthenticationProvider
### 5-1
- OAuth
  - Provider -> Resource Owner -> Client
  - authorization code 授權碼模式
  - resource owner password credentials 密碼模式
  - client credentials 客戶端模式
  - implicit 簡化模式
### 5-2
- ServiceProvider(AbstractOAuth2ServiceProvider)
  - OAuth2Operations(OAuth2Template)
  - Api(AbstractOAuth2ApiBinding)
- ConnectionFactory(OAuth2ConnectionFactory)
- Connection(OAuth2Connection)
- UsersConnectionRepository(JdbcUsersConnectionRepository)
### 5-4
- SocialUserDetailsService
