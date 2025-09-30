```java
package com.api.service.aspect;

import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.util.Arrays;

import javax.servlet.http.HttpSession;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.api.service.dto.BaseDto;

/**
 *
 *
 * @author Jay
 * @date 2019-08-14
 */
@Component
@Aspect
public class ServiceAspect {

	@Autowired
	private HttpSession httpSession;

	// @formatter:off
	@Pointcut("execution(* com.api.service.service.imp.*Service.save*(..)) "
			+ "|| execution(* com.api.service.service.imp.*Service.update*(..)) "
			+ "|| execution(* com.api.service.service.imp.*Service.delete*(..))")
	// @formatter:on
	private void pointcut() {
	}

	@Around("pointcut()")
	private Object setUserName(ProceedingJoinPoint joinPoint) throws Throwable {

		Object[] args = joinPoint.getArgs();

		MethodSignature signature = (MethodSignature) joinPoint.getSignature();
		String methodName = signature.getMethod().getName();

		if (isProcessMethod(joinPoint)) {
			return joinPoint.proceed(args);
		}

		Class<?> parent = args[0].getClass().getSuperclass();

		if (methodName.contains("save")) {
			Field createUser = parent.getDeclaredField("createUser");
			createUser.setAccessible(true);
			createUser.set(args[0], httpSession.getAttribute("name"));
		}

		Field updateUser = parent.getDeclaredField("UpdateUser");
		updateUser.setAccessible(true);
		updateUser.set(args[0], httpSession.getAttribute("name"));

		return joinPoint.proceed(args);
	}

	private boolean isProcessMethod(ProceedingJoinPoint joinPoint) {
		MethodSignature signature = (MethodSignature) joinPoint.getSignature();
		Method method = signature.getMethod();
		String methodName = method.getName();
		Object[] args = joinPoint.getArgs();
		Class<?>[] paramTypes = method.getParameterTypes();

		String[] list = new String[] { "save", "update", "delete" };
		boolean isMatchMethodName = Arrays.stream(list).anyMatch(methodName::contains);

		return (0 == args.length || !isMatchMethodName || !BaseDto.class.isAssignableFrom(paramTypes[0]));
	}
}

```

