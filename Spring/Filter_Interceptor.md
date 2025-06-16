# Filter, Servlet, Interceptor

> #### 실행 순서
>
> 클라이언트 요청<br>
> ↓<br>
> 필터 (Filter) – 서블릿 컨테이너 수준<br>
> ↓<br>
> DispatcherServlet<br>
> ↓<br>
> 인터셉터 (Interceptor) – 스프링 MVC 수준<br>
> ↓<br>
> 컨트롤러

## Survelet

- 역할: HTTP 요청을 받아 처리하고 응답을 반환하는 자바 클래스.
- 사용 위치: 클라이언트의 요청을 직접 받아 로직을 처리.
- 예시: HttpServlet 상속하여 doGet, doPost 메서드 구현.
- 실행 시점: 클라이언트의 요청이 서블릿 URL에 매핑될 때 실행.

## Filter

- 역할: 서블릿 실행 전/후로 공통 처리(로그, 인코딩, 인증 등)를 수행하는 서블릿 사전/사후 처리기.
- 사용 위치: DispatcherServlet 이전 단계에서 작동 (서블릿 컨테이너 수준, 톰캣 등).

```java
void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
```

- ServletRequest는 기능이 적어 HttpServletRequest로 다운캐스팅 필요
- chain.doFilter를 호출하지 않으면 다음 필터나 서블릿으로 전달되지 않음

```java
@WebFilter(urlPatterns = "/*") // 모든 요청에 대해 필터 적용
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
        throws IOException, ServletException {

        HttpServletRequest httpReq = (HttpServletRequest) request;
        System.out.println("필터 실행: " + httpReq.getRequestURI());

        chain.doFilter(request, response); // 다음 필터 또는 서블릿 호출

        System.out.println("필터 후처리 완료");
    }
}
```

## Interceptor

- 역할: 컨트롤러 실행 전/후에 요청을 가로채고 공통 처리를 수행하는 스프링 전용 기능.
- 사용 위치: DispatcherServlet 이후, 컨트롤러 이전/이후.

```java
boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)
void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
```

- return 값에 따라서 다음 인터셉터나 컨트롤러로 전달 여부 결정
- false 반환 시 요청 처리 중단

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class AuthInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {

        System.out.println("인터셉터 preHandle: 인증 체크");

        // 인증 예시
        if (request.getSession().getAttribute("user") == null) {
            response.sendRedirect("/login");
            return false;
        }

        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response,
                           Object handler, ModelAndView modelAndView) {
        System.out.println("인터셉터 postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
                                Object handler, Exception ex) {
        System.out.println("인터셉터 afterCompletion");
    }
}

```

### WebConfig

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

	// filter 등록
    @Bean
    public FilterRegistrationBean<LoggingFilter> loggingFilter() {
        FilterRegistrationBean<LoggingFilter> registrationBean = new FilterRegistrationBean<>();
        registrationBean.setFilter(new LoggingFilter());
        registrationBean.setOrder(1);
        registrationBean.addUrlPatterns("/*");
        return registrationBean;
    }

	// interceptor 등록
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new AuthInterceptor())
				.setOrder(2) // 인터셉터 실행 순서
				.addPathPatterns("/secure/**"); // /secure 하위 URL에만 적용
	}
}
```

### 비교 요약

| 구분      | 필터(Filter)                | 인터셉터(Interceptor)          | 서블릿(Servlet)           |
| --------- | --------------------------- | ------------------------------ | ------------------------- |
| 레벨      | 서블릿 컨테이너             | 스프링 프레임워크              | 서블릿 컨테이너           |
| 위치      | DispatcherServlet 이전      | DispatcherServlet 이후         | DispatcherServlet 내부    |
| 목적      | 요청 전처리/후처리          | 컨트롤러 전후 처리             | 비즈니스 로직 처리        |
| 설정 방식 | `web.xml` or `@WebFilter`   | `WebMvcConfigurer` 등록        | `@WebServlet` or XML 설정 |
| 주 용도   | 인코딩, 보안, 로깅, CORS 등 | 인증, 세션, 로깅, 권한 체크 등 | 요청에 따른 응답 처리     |

- 필터가 필요한 경우가 아니면 주로 인터셉터를 사용
