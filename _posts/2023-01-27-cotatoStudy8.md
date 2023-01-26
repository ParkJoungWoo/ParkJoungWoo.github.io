---
layout: post
title: "Spring security 2편"
subtitle: 스프링 3주차
categories: spring
tags: [spring]
---
⚠️ 본 게시물은 `스프링 부트와 AWS로 혼자 구현하는 웹서비스`를 참고도서로 활용합니다. 저작권은 본 책의 저서에게 있음을 알립니다.

## 5장 스프링 시큐리티

### 지난 시간엔...

1. 이 서비스는 유저가 게시물을 등록하는 기능이 있다.
2. 유저는 아이디, 이름, 이메일, 사진으로 구별된다.
3. OAuth를 통해 로그인 과정을 Google, Naver에 위임한다.
4. 사용자의 status(Role)에 따라 기능이 제한된다.

위와 같은 기능을 가진 서비스를 구축하려고 합니다.

또한 인증, 인가에 관련된 기능은 Spring Security의 도움을 받으려고 합니다.

### Entity가 있으면?

저번 시간에 만든 User 클래스는 Entity클래스 입니다.

<iframe src="https://giphy.com/embed/M1VL81pAxfj3y" width="480" height="343" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/anthony-cheating-carmelo-M1VL81pAxfj3y">via GIPHY</a></p>

Entity 클래스가 있다면 그 정보를 송수신 하기 위한 DTO 클래스도 필요합니다. 또한 저장소에서 User 클래스를 CRUD해야 하기 때문에 Repository Class도 필요합니다.

### 의존성 추가하기
실질적으로 Google OAuth2 기능을 사용하고 싶다는 뜻은 다시 말하면 기능 사용을 위한 의존성이 필요하다는 뜻입니다.

```
implementation('org.springframework.boot:spring-boot-starter-oauth2-client')
```
의존성을 추가해 줍시다.

### Customizing
저번 시간에 설명한 Spring Security의 장점은 `커스터 마이징`이 가능하다는 것입니다.

그래서 나의 Spring 프로젝트에 대한 Spring Security 설정을 사용자 정의대로 변경할 수 있습니다.

```
스프링 시큐리티 설정을 위해서는
WebSecurityConfigurerAdapter라는 WebSecurityConfigurer를 만들기 위한 추상 클래스를 구현해야 합니다.

WebSecurityConfigurer는 WebSecurity를 Custom하기 위한 인터페이스 입니다.

WebSecurity는 SpringSecurity, Filter Chain(DelegatingFilterProxy)을 생성합니다..
```
위와 같이 논지와 벗어나고 지금은 Spring의 숲을 보는 과정이기 때문에 우선은 `Spring Security를 설정하기 위한 클래스`라고 정의하겠습니다.

```java
@RequiredArgsConstructor
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    private final CustomOAuth2UserService  customOAuth2UserService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .headers().frameOptions().disable()
                .and()
                    .authorizeRequests()
                    .antMatchers("/", "/css/**", "/images/**", "/js/**", "/h2-console/**").permitAll()
                    .antMatchers("/api/v1/**").hasRole(Role.USER.name())
                    .anyRequest().authenticated()
                .and()
                    .logout()
                        .logoutSuccessUrl("/")
                .and()
                    .oauth2Login()
                        .userInfoEndpoint()
                            .userService(customOAuth2UserService);
    }
}
```
여기에서 주목해야할 점을 크게 3가지로 보겠습니다.

1. 공격 및 권한

위 클래스는 HttpSecurity라는 http관련 설정 객체를 설정을 합니다.

h2-console을 사용하기 때문에 csrf(사이트 간 요청 위조)방지 옵션을 사용하지 않고 또한 headers().frameOptions()-(X-Frame-Options Click jacking)방지 옵션을 모두 disable화 합니다.

2. 로그아웃을 했을 경우 "/"로 리다이렉트 됩니다.
3. 로그인이 성공한 경우 oauth2Login()을 거쳐 엔드포인트를 설정하고 customOAuth2UserService 서비스를 제공합니다.)


