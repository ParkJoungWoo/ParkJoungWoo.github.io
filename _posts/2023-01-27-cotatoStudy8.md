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
위와 같이 요소별로 알아보는 것은 논지와 벗어나고 지금은 Spring의 숲을 보는 과정이기 때문에 우선은 `Spring Security를 설정하기 위한 클래스`라고 정의하겠습니다.

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

- 공격 및 권한

위 클래스는 HttpSecurity라는 http관련 설정 객체를 설정을 합니다.

h2-console을 사용하기 때문에 csrf(사이트 간 요청 위조)방지 옵션을 사용하지 않고 또한 headers().frameOptions()-(X-Frame-Options Click jacking)방지 옵션을 모두 disable화 합니다.

- 로그아웃을 했을 경우 "/"로 리다이렉트 됩니다.
- 로그인이 성공한 경우 oauth2Login()을 거쳐 엔드포인트를 설정하고 customOAuth2UserService 서비스를 제공합니다.)

### 커스텀 OAuth 서비스 정의하기
```
✔️사용자 Entity 정의하기
✔️Spring Security 권한(Role) 정의하기
✔️Spring Security Config
❎OAuth2서비스 정의
❎화면구현하기
```
이제 스프링에 대한 설정을 완료하였습니다.

그렇다면 구글에서 OAuth로 회원 정보를 받은 이후 세션 저장, 저장소에 회원정보 등록 등 OAuth와 관련된 서비스를 정의해보겠습니다.

```java
@RequiredArgsConstructor
@Service
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
    private final UserRepository userRepository;
    private final HttpSession httpSession;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
        OAuth2UserService<OAuth2UserRequest, OAuth2User> delegate = new DefaultOAuth2UserService();
        OAuth2User oAuth2User = delegate.loadUser(userRequest);
        String registrationId = userRequest.getClientRegistration().getRegistrationId();
        String userNameAttributeName = userRequest.getClientRegistration()
            .getProviderDetails()
            .getUserInfoEndpoint()
            .getUserNameAttributeName();
        OAuthAttributes attributes = OAuthAttributes.of(registrationId, userNameAttributeName, oAuth2User.getAttributes());

        User user = saveOrUpdate(attributes);
        httpSession.setAttribute("user", new SessionUser(user));
        return new DefaultOAuth2User(
            Collections.singleton(
                new SimpleGrantedAuthority(user.getRoleKey())),
                attributes.getAttributes(),
                attributes.getNameAttributeKey());

    }

    private User saveOrUpdate(OAuthAttributes attributes) {
        User user = userRepository.findByEmail(
            attributes.getEmail())
        .map(
            entity -> entity.update(attributes.getName(), attributes.getPicture()))
            .orElse(attributes.toEntity());
        return userRepository.save(user);
    }
}
```

코드가 길지만 뜯어 보면서 확인해봅시다.
```java
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
    private final UserRepository userRepository;
    private final HttpSession httpSession;
    ...
```

1. 현재 작성하고 있는 기능은 서비스이기 때문에 @Service 어노테이션을 붙여줍니다.
2. 또한 스프링에서 인터페이스로 OAuth서비스를 정의하고 있기 때문에 `OAuth2UserService`를 implements하는 것을 알 수 있습니다.
3. 유저의 정보를 DB에 저장해야 하기 때문에 Repository를 등록합니다.
4. Session 방식으로 회원의 정보를 접근하기 위해 HttpSession 또한 등록합니다.

-> *이제 회원 정보를 DB에 저장할 수 있고 Session에서 회원 정보를 접근할 수 있습니다.*

```java
@Override
public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
    OAuth2UserService<OAuth2UserRequest, OAuth2User> delegate = new DefaultOAuth2UserService();
    OAuth2User oAuth2User = delegate.loadUser(userRequest);
    String registrationId = userRequest.getClientRegistration().getRegistrationId();
    String userNameAttributeName = userRequest.getClientRegistration()
        .getProviderDetails()
        .getUserInfoEndpoint()
        .getUserNameAttributeName();
    OAuthAttributes attributes = OAuthAttributes.of(registrationId, userNameAttributeName, oAuth2User.getAttributes());

    User user = saveOrUpdate(attributes);
    httpSession.setAttribute("user", new SessionUser(user));
    return new DefaultOAuth2User(
        Collections.singleton(
            new SimpleGrantedAuthority(user.getRoleKey())),
            attributes.getAttributes(),
            attributes.getNameAttributeKey());

}
```
1. 위에서 언급한 인터페이스 OAuth2UserService는 loadUser를 정의하고 있습니다. 그래서 @Override를 합니다.
2. OAuth2UserRequest는 쉽게 말해서 endpoint에서 유저 정보를 접근하기 위한 객체입니다. 유저 정보를 활용해야 하기 때문에 이 객체를 인자로 받습니다.
3. OAuth2UserService의 구현 클래스인 DefaultOAuth2UserService의 객체 delegate(서비스 위임자)를 정의합니다.
4. delegate를 통해 유저의 정보를 OAuth2User 객체로 생성합니다.
   - 이 또한 delegate의 메소드인 loadUser를 활용합니다.
5. registrationId는 Client(여기서는 Google)의 등록 ID입니다.
   - userNameAttributeName은 위에서 받은 userRequest의 endpoint에서 받은 유저의 이름입니다.
   - 모두 유저의 세션, DB 저장을 위해 필요합니다.
6. OAuthAttributes는 위와 같은 attribute들을 DTO로 만든 클래스 입니다.
   - 등록id, 유저name, Oauth2User객체의 getAttributes(Map을 리턴)로 선언하는 점만 알고 갑시다.
7. 아직 작성하지 않았지만 saveOrUpdate는 JPA의 기능을 활용해 유저의 정보를 저장하는 것으로 미리 정의합니다.
8. session에서 유저의 정보를 접근할 일이 있기 때문에 세션에 유저 정보(로그인 중인)를 설정합니다.
9. 위와 같은 값을 바탕으로 DefaultOAuth2User라는 OAuth2User 인터페이스를 구현한 클래스의 객체를 싱글톤으로 생성하여 반환합니다.

정말 복잡한 코드이고 이해하기 난해하지만 지금은 `로그인한 나의 정보를 저장소에 등록하고 세션에 등록한다` 정도로 이해하면 됩니다.

```java
private User saveOrUpdate(OAuthAttributes attributes) {
    User user = userRepository.findByEmail(attributes.getEmail())
            .map(entity -> entity.update(attributes.getName(), attributes.getPicture()))
            .orElse(attributes.toEntity());
    return userRepository.save(user);
}
```

JPA의 영속성을 통해 값이 수정되면 update를, 존재하지 않으면 create을 합니다.

그런데 유심히 보니 이해가 되지 않는 부분이 있습니다.

```java
httpSession.setAttribute("user", new SessionUser(user));
```
왜 굳이 User 클래스를 사용하지 않고 SessionUser라는 새로운 클래스를 정의해서 httpSession에 설정했을 까요?

바로 User 클래스가 Entity이기 때문입니다. 또한 Session에는 인증에 관련한 정보만 필요하기 때문에 User의 모든 필드가 필요하지 않습니다.

이 의문점은 다음 시간에 확인하도록 하겠습니다.

-----------------------------------------

위에서 attribute들을 데이터 교환에 사용하기 위한 DTO가 OAuthAttributes 라고 했습니다.

attribute들이 있으면 유저를 생성할 수 있으므로 이 클래스를 통해 유저를 생성하고 Entity로 만들려고 합니다.

```java
@Getter
public class OAuthAttributes {
    private Map<String, Object> attributes;
    private String nameAttributeKey;
    private String name;
    private String email;
    private String picture;
    @Builder
    public OAuthAttributes(Map<String, Object>attributes,
                           String nameAttributeKey, String name,
                           String email, String picture) {
        this.attributes = attributes;
        this.nameAttributeKey = nameAttributeKey;
        this.name = name;
        this.email = email;
        this.picture = picture;
    }
    public static OAuthAttributes of(String registrationId,
                                     String userNameAttributeName,
                                     Map<String, Object> attributes) {
        if ("naver".equals(registrationId)) {
            return ofNaver("id", attributes);
        }
        return ofGoogle(userNameAttributeName, attributes);
    }

    public static OAuthAttributes ofGoogle(String userNameAttributeName,
                                           Map<String, Object>attributes){
        return OAuthAttributes.builder()
                .name((String) attributes.get("name"))
                .email((String) attributes.get("email"))
                .picture((String) attributes.get("picture"))
                .attributes(attributes)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }

    public static OAuthAttributes ofNaver(String userNameAttributeName,
                                          Map<String, Object>attributes){
        Map<String, Object> response = (Map<String, Object>) attributes.get("response");
        return OAuthAttributes.builder()
                .name((String) response.get("name"))
                .email((String) response.get("email"))
                .picture((String) response.get("picture"))
                .attributes(response)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }
    public User toEntity(){
        return User.builder()
                .name(name)
                .email(email)
                .picture(picture)
                .role(Role.GUEST)
                .build();
    }
}
```
<iframe src="https://giphy.com/embed/BcMJvmwkmbyWpKkBj3" width="480" height="400" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/theoffice-nbc-the-office-tv-BcMJvmwkmbyWpKkBj3">via GIPHY</a></p>

만만치 않은 코드 길이네요 메소드 별로 살펴보겠습니다.

1. 이 DTO 객체를 정의하기 위한 생성자가 필요하기 때문에 기본 생성자를 작성했습니다.
2. of : 사용자가 OAuth Client를 누구로 했는지에 따라서 가지고 있는 Attribute가 달라질 수 있기 때문에 `of`메소드에서 조건문을 통해 다른 객체를 리턴합니다.
3. ofGoogle : 만약 제공자가 Google일 경우 위와 같은 build 패턴으로 객체를 생성해 리턴합니다.
4. ofNaver : 만약 제공자가 Naver일 경우 위와 같은 build 패턴으로 객체를 생성, 리턴합니다. Google과 다른 점은 response를 받는다는 점입니다.
5. toEntity : User 생성자에 attribute가 들어갈 수 있도록 Entity화하는 메소드도 작성합니다.