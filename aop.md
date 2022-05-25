### 注解说明

```
除了@Around外，每个方法里都可以加或者不加参数JoinPoint。

JoinPoint里包含了类名、被切面的方法名，参数等属性，可供读取使用。

@Around参数必须为ProceedingJoinPoint，pjp.proceed相应于执行被切面的方法。

@AfterReturning方法里，可以加returning = “xxx”，xxx即为在controller里方法的返回值，本例中的返回值是”first controller”。

@AfterThrowing方法里，可以加throwing = “XXX”，读取异常信息，如本例中可以改为：
```

### 执行顺序

```
1. @Around ProceedingJoinPoint.proceed()之前
2. @Before
3. @AfterReturning / @AfterThrowing
4. @After
5. @Around ProceedingJoinPoint.proceed()之后
```

### @Pointcut常用 

```java
// controller包下所有方法
@Pointcut("execution (* com.example.xjb.controller..*(..))")
// controller包下所有private方法
@Pointcut("execution (private * com.example.xjb.controller..*(..))")
// test方法
@Pointcut("execution(* com.example.xjb.controller.TestController.test())")
```

### example

切面类

```java
@Aspect
@Component
public class TestAOP {

    @Pointcut("execution (* com.example.xjb.controller.TestController.*(..))")
    public void pointCut(){}

    @Before("pointCut()")
    public void doBefore(JoinPoint joinPoint) {
        System.out.println(joinPoint.getSignature().getDeclaringTypeName());
        System.out.println(joinPoint.getSignature().getName());
    }

    @After("pointCut()")
    public void doAfter(){
        System.out.println("after");
    }
	// 可以使用returning获取结果
    @AfterReturning(returning = "result",pointcut = "pointCut()")
    public void doAfterReturning(Object result){
        System.out.println("after finish, return value: " + result);
    }

    @AfterThrowing(throwing = "e",pointcut = "pointCut()")
    public void afterThrowing(Exception e){
        System.out.println("throw" + e.getMessage());
    }

    //环绕通知,环绕增强，相当于MethodInterceptor
    @Around("pointCut()")
    public Object arround(ProceedingJoinPoint pjp) {
        System.out.println("方法环绕start.....");
        try {
            System.out.println("proceed()前");
            Object o =  pjp.proceed();

            System.out.println("方法环绕proceed，结果是 :" + o);
            return o;
        } catch (Throwable e) {
            e.printStackTrace();
            return null;
        }
    }
    
    @Pointcut(value = "@annotation(com.example.xjb.annotation.Test)")
    public void annotationCut(){}

    @Around("@annotation(test)")
    public Object around(ProceedingJoinPoint pjp, Test test) {
        //获取注解里的值
        System.out.println("second around:" + test.name());
        try {
            return pjp.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            return null;
        }
    }
}
```

---

实现类

```java
@RestController
@AllArgsConstructor
public class TestController {
    @GetMapping("/test")
    public String test(){

        System.out.println("in the body");

        return "docker hub test";
    }

    @Test(name = "lzr")
    @GetMapping("/test2")
    public String test2() {

        System.out.println("in the body2");

        return "docker hub test222";
    }
}
```

---

注解类

```java
@Target({ElementType.FIELD,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
    String name() default "kg";
}
```



[参考](https://www.jianshu.com/p/dd3911c29f15)

