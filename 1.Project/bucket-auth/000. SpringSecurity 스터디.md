### spring security 의존성
- **security 의존성을 설정할 경우, 해당 서버는 모든 경로에 대해 인증이 필요한 서버로 기능**하게 된다.
- 아래와 같은 형태로 시큐리티 전용 설정 파일을 생성할 경우, 명시된 스프링 시큐리티 필터가 스프링 필터 체인에 등록된다.
```kotlin
@Configuration  
@EnableWebSecurity // 스프링 시큐리티 필터가 스프링 필터체인에 등록됨  
class SecurityConfig {  
    @Bean  
    fun filterChain(http: HttpSecurity): SecurityFilterChain = http.run {  
        csrf { it.disable() }  
        authorizeHttpRequests {  
            it                // 로그인 사용자만 허용  
                .requestMatchers("/user/**").authenticated()  
                // 로그인 사용자 + admin + manager 권한만 허용  
                .requestMatchers("/manager/**").access(  
                    WebExpressionAuthorizationManager("hasRole('ROLE_ADMIN') or hasRole('ROLE_MANAGER')")  
                )  
                // 로그인 사용자 + admin 권한만 허용  
                .requestMatchers("/admin/**").access(  
                    WebExpressionAuthorizationManager("hasRole('ROLE_ADMIN')")  
                )  
                // 그 외 모든 요청은 허용  
                .anyRequest().permitAll()  
        }  
  
        // 로그인 페이지  
        formLogin { it.loginPage("/login") }  
  
        build()  
    }  
}
```

### 컨트롤러 메소드에 대한 인증 적용
임의의 컨트롤러 메소드에만 어떠한 권한을 가진 사용자에 대해 접근을 허용하고자 하는 경우, 아래와 같이 간단하게 작성할 수 있다.
```kotlin
@Controller  
class IndexController {  
    @Secured("ROLE_ADMIN") // 이렇게!
    @GetMapping("/info")  
    @ResponseBody  
    fun info(): String {  
        return "개인정보"  
    }  
    
	@PreAuthorize("hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')") // 또는 이렇게!
	@GetMapping("/data")  
	@ResponseBody  
	fun data(): String {  
	    return "데이터 정보"  
	}
}
```
상술한 두 방식은 각각 대응되는 설정이 있으며, 이는 아래와 같다.
```kotlin
@Configuration  
@EnableWebSecurity // 스프링 시큐리티 필터가 스프링 필터체인에 등록됨  
@EnableMethodSecurity(securedEnabled = true, prePostEnabled = true) // 1. secured 어노테이션을 활성화한다! 2. preAuthorize 어노테이션을 활성화한다!  
class SecurityConfig {  
	// ...생략
}
```

유사한 개념으로 `@PostAuthorize`도 있으나 이는 잘 사용되지 않으며, 일반적으로는 `@PreAuthorize`보다 `@Secured`가 자주 사용된다. **이러한 방식은 임의의 컨트롤러 메소드에만 인증을 적용하고자 하는 경우에 활용하며, 그렇지 않은 경우에는 `authorizeHttpRequests`를 활용하는 글로벌 설정을 적용하는 것이 권장**된다.

### oauth 설정
#### 승인된 리디렉션 URI란?
- 구글 로그인 완료 후, 구글 서버에서 리다이렉트해주는 URI
- 인증이 되었음을 의미하는 코드를 반환하고, 웹 서버는 해당 코드를 활용하여 구글에 다시 액세스 토큰을 요청함
	- 액세스 토큰을 기반으로 민감한 정보에 접근하게 될 것!
- oauth client 라이브러리를 사용하는 경우, `http://${서버주소}/login/oauth2/code/google` 형태의 URI는 사실상 고정되어야 하므로 이렇게 작성한다!
	- 이 경우, 해당 주소를 처리하는 컨트롤러를 만들어줄 필요조차 없다!
#### 구글 로그인 프로세스
1. 코드 수신: 정상적인 사용자로서 인증이 되었음을 의미한다.
2. 액세스 토큰 획득: 사용자 정보 접근 등의 권한이 부여됨을 의미한다.
3. 사용자 프로필 정보 획득
4. 사용자 프로필 정보를 기반으로 회원가입 처리
	1. 또는 주소 등의 추가적인 정보가 필요한 경우, 추가 회원가입 절차 필요
**구글 로그인의 경우, 코드를 수신하는 절차 없이 액세스 토큰과 사용자의 프로필 정보를 한 번에 수신**한다.


#### DefaultOAuth2UserService
자신만의 userService를 정의한 경우 로그인 후처리 과정을 구현할 수 있으며, 크게 다음과 같은 정보를 활용할 수 있다.
1. clientRegistration: registrationId를 기반으로 어떤 OAuth 플랫폼을 활용하여 로그인했는지 확인 가능하다.(ex: google)
2. userRequest: 구글 로그인을 통해 코드를 반환 받고, oauth client 라이브러리가 이를 활용하여 액세스 토큰을 획득하기까지의 정보
3. loadUser 메소드: userRequest에 포함된 정보를 활용하여 구글로부터 회원 프로필을 조회하기 위해 사용하는 메소드

#### Authentication 객체가 가질 수 있는 타입 두가지
- 스프링 시큐리티는 기본적으로 자기만의 세션인 시큐리티 세션을 두며, 이는 서버 자체적으로 관리하는 세션 영역 내부의 일부를 차지한다.
- 이러한 시큐리티와 관련된 세션에 저장될 수 있는 것은 `Authentication` 타입의 객체 뿐이다.
	- 이 때, 이렇듯 시큐리티 세션 영역에 저장된 `Authentication` 객체는 컨트롤러의 메소드 인자로 DI 받는 것이 가능하다.
- **`Authentication` 객체에 저장 가능한 타입은 `UserDetails` 또는 `OAuth2User` 의 단 두가지 타입** 뿐이다.
결국 **시큐리티가 유지하는 세션에는 반드시 `Authentication` 객체만 저장될 수 있으며, `Authentication` 객체가 시큐리티 세션 영역에 저장된 순간 로그인이 된 것으로 이해**할 수 있다.
다시 `Authentication` 타입에는 일반적으로 구현하는 로그인을 의미하는 `UserDetails` 타입의 데이터와, 구글 로그인 등의 OAuth 기반 로그인을 의미하는 `OAuth2User` 타입의 데이터 중 하나가 저장될 수 있다.
이렇듯 **각 로그인 방식을 두 타입으로 분리하는 것은 하나의 코드로 모든 것을 관리하는 데에 불편함**을 준다!
- **해결 방법은 간단하게도, 두 타입을 공통적으로 확장하는 별도의 클래스를 작성**하는 데에 있다.

#### @AuthenticationPrincipal 어노테이션의 활성화 시점
- loadUser(OAuth 기반 로그인)또는 loadUserByUsername(일반 로그인) 등의 메소드에 의해 반환되는 객체는 `Authentication` 객체에 저장되므로, 각 로그인 유형 별로 동일한 객체를 사용하고자 한다면 관련된 서비스를 재정의해주어야 한다.
	- 바꿔 말해, 서비스를 재정의하지 않더라도 loadUser 류의 메소드는 기본적인 동작을 갖는다!
- **Oauth 기반의 로그인을 예로 들어, 로그인시 강제로 회원가입을 시키거나 구체 반환 타입을 커스터마이징하고자 하는 경우에는 `DefaultOAuth2UserService`와 같이 관련된 서비스의 재정의가 필요**하다.