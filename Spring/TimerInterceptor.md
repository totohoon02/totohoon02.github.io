# Timer with Interceptor & Annotation

### Timer.java

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME) // alive on runtime
public @interface Timer {
}
```

### TimerInterceptor.java

```java
@Slf4j
public class TimerInterceptor implements HandlerInterceptor {
	
	private final String TIMER_KEY = "timer";
	
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		if (handler instanceof HandlerMethod) {
			HandlerMethod hm = (HandlerMethod) handler;

      // Method Level
			Timer timer = hm.getMethodAnnotation(Timer.class);

      // Type Level
			if(timer == null){
				timer = hm.getBeanType().getAnnotation(Timer.class);
			}
			
			if (timer != null) {
				request.setAttribute(TIMER_KEY, System.currentTimeMillis());
			}
		}
		return true;
	}
	
	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
		HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
	}
	
	@Override
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
		if (handler instanceof HandlerMethod) {
			HandlerMethod hm = (HandlerMethod) handler;
			Timer timer = hm.getMethodAnnotation(Timer.class);
			
			if(timer == null){
				timer = hm.getBeanType().getAnnotation(Timer.class);
			}
			
			if (timer != null) {
				Long startTime = (Long) request.getAttribute(TIMER_KEY);
				if (startTime != null) {
					long duration = System.currentTimeMillis() - startTime;
					System.out.println(hm.getMethod().getName() + " 처리 시간: " + duration + "ms");
				}
			}
		}
	}
}
```

### WebConfig.java
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
	
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		// Annotation based
		registry.addInterceptor(new TimerInterceptor());

	}
}
```

