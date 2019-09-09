# @Valid

如果接口的形参使用了被`@NotNull`等Validation注解修饰了的类的字段，那么需要在实体类之前加上`@Valid`注解，Validation相关的注解才会生效。

```java
/**
     * 如果是对对象校验需要在对象前面加{javax.validation.@Valid}注解,并且在对象后面紧跟着{org.springframework.validation.BindingResult}对象,
     *
     * @param i
     * @param
     */
@GetMapping("say-entity")
public void sayParam(@Valid HelloEntity i, Errors errors) {
    if (errors.hasErrors()) {
        errors.getAllErrors().forEach(allError -> {
            System.out.println(allError.toString());
            throw new IllegalArgumentException(allError.getDefaultMessage());
        });
    }
}
```



# BindingResult和Errors

BindingResult和Errors只能跟在实体类后面，如果跟在非实体类后面会产生`java.lang.IllegalStateException`的异常。

```java
/**
     * a---An Errors/BindingResult argument is expected to be declared immediately after the model attribute,
     * the @RequestBody or the @RequestPart arguments to which they apply:
     * public java.lang.String com.qubaba.controller.TestBindingController
     * .test1(java.lang.String,org.springframework.validation.BindingResult)
     *
     * @param param
     * @param bindingResult
     * @return
     */
@RequestMapping("test")
public String test1(@NotBlank(message = "不能为空") String param, BindingResult bindingResult) {
    System.out.println(bindingResult.getErrorCount());
    return param;
}
```

# @Validated

如果接口的形参使用了`@NotNull`等Validation的注解，那么需要在类上添加`@Validated`注解，Validation的相关注解才能生效。

```java
@Validated
public class TestBindingController {

    @RequestMapping("test2")
    public String test2(@NotBlank(message = "不能为空") String param) {
        return param;
    }
}

```

