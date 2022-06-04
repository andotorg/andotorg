---
title: Java自定义注解学习写一个小例子
date: 2018-02-22 21:37:28
tags: java, annotation
---

# Java自定义注解书写例子学习

## 1、先新建一个注解TestC

```base
@Target({ ElementType.TYPE, ElementType.METHOD })   //说明次注解可以作用于类和方法
@Retention(RetentionPolicy.RUNTIME)  //说明此类是从JVM运行开始使用
public @interface TestC {
	/**
	 * 获取被注解方法的参数
	 * 
	 * */
	String value() default "";    //方法名称自己定义，返回值根据需要
}
```

## 2、然后新建一个AOP拦截器进行拦截注解和备注解的方法

```base
@Aspect
@Component
public class ParamInterception {
	
	@Pointcut("execution(* cn.zup.*.service.*.*(..)) && @annotation(cn.zup.framework.aop.TestC)")
	private void anyMethod(){}
	
	@Before(value="anyMethod()")
	public void before(JoinPoint joinPoint){
		System.err.println("================================================================");
		Signature signature = joinPoint.getSignature();
        MethodSignature methodSignature = (MethodSignature) signature;
        Method method = methodSignature.getMethod();
        System.err.println(method.getName());
        Class<?> c = joinPoint.getClass();
		// 获取类中的所有定义字段    
        Field[] fields = c.getDeclaredFields();
        // 循环遍历字段，获取字段对应的属性值    
        for (Field field : fields) {    
            // 如果不为空，设置可见性，然后返回    
            field.setAccessible(true); 
            try {    
                // 设置字段可见，即可用get方法获取属性值。    
            	if(field.get(joinPoint) != null){
            		System.err.println(field.getName() + "==============" + field.get(joinPoint));
            	}
            } catch (Exception e) {    
            	System.err.println("error--------"+this.getClass().getMethods()+".Reason is:"+e.getMessage());    
            }    
        }   
        
	}
	
}
```

## 在方法上进行添加注解

```
@TestC
public String method(){
    //TODO IS CODING
}
```

> 以上就是一个最简单的注解的写法


