---
layout: post
title: "Spring security 3편"
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
5. OAuth2로 로그인한 정보를 받아오는 기능

위와 같은 기능을 가진 서비스를 구축하려고 합니다.

### Serializable
이전 시간에 User 클래스 대신 SessionUser 클래를 사용하는 이유를 설명드렸는데요.

1. User 클래스는 Entity 클래스이다
2. User 클래스의 모든 필드값이 필요하지 않다.

위와 같은 이유가 있습니다.

또한 `Serializable`을 진행해야만 Session에 사용자의 정보를 저장할 수 있기 때문입니다.

Serializable의 장점은 무엇이 있을까요?

```
easily restored to a byte stream, allows the state of an object to be saved to a file, sent over a network, or stored in DB
```
직렬화는 클래스가 바이트 스트림으로 변환할 수 있음을 의미합니다.\
java에서는 implement Serializable로 기능을 사용할 수 있고 이를 통해 같은 java 시스템 간 데이터 교환을 용이하게 할 수 있습니다.

### 그렇다면 User 클래스에 직접 직렬화를 구현하면 안되나요?
User 클래스는 Entity 클래스 입니다.

Entity 클래스는 어떠한 관계가 형성될 지 모릅니다. 그 상황에서 직접 Entity 클래스를 사용하는 것보다 Dto를 생성하는 것이 안전합니다.

### 화면 구성
이제는 화면을 구성해보겠습니다.
```html
<!DOCTYPE HTML>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
<div th:replace="fragments/header.html :: fragment-header"></div>
<body>
<h1>스프링 부트로 시작하는 웹서비스</h1>
<div class="col-md-12">
<!--    로그인 기능 영역-->
    <div class="row">
        <div class="col-md-6">
            <a href="/posts/save" type="button" class="btn btn-primary">글 등록</a>
            <th:block th:if="${not #strings.isEmpty(userName)}">
                Logged in as <span th:text="${userName}"/>
                <a href="/logout" class = "btn btn-info active" role="button">Logout</a>
            </th:block>
            <th:block th:unless="${not #strings.isEmpty(userName)}">
                <a href="/oauth2/authorization/google" class="btn btn-success active" role="button">Google Login</a>
                <a href="/oauth2/authorization/naver" class="btn btn-success active" role="button">Naver Login</a>
            </th:block>
        </div>
    </div>
</div>
<div th:replace="fragments/footer.html :: fragment-footer"></div>
</body>
</html>
```
이전 html 코드와 비슷한데 로그인 부분만 살펴보겠습니다.
```html
<th:block th:if="${not #strings.isEmpty(userName)}">
    Logged in as <span th:text="${userName}"/>
    <a href="/logout" class = "btn btn-info active" role="button">Logout</a>
</th:block>
<th:block th:unless="${not #strings.isEmpty(userName)}">
    <a href="/oauth2/authorization/google" class="btn btn-success active" role="button">Google Login</a>
    <a href="/oauth2/authorization/naver" class="btn btn-success active" role="button">Naver Login</a>
</th:block>
```
1. 타임리프 문법을 사용하여 유저의 이름이 존재한다면 로그인 유저의 이름을 출력하고 로그아웃 버튼이 보이도록 하였습니다.
   - 로그아웃을 할 경우는 "/logout"의 url로 이동합니다.
2. 유저의 이름이 없다면 로그인하지 않은 것으로 판단하여 로그인 버튼이 드러나게 하였습니다.
   - Naver 로그인은 차후에 추가할 것이므로 현재는 버튼만 구현하였습니다.

### 로그인관련 컨트롤러 수정하기
화면을 구성하였으니 컨트롤러를 통해 로그인 기능을 추가하도록 하겠습니다.

```java
@RequiredArgsConstructor
@Controller
@RequestMapping(value="/")
public class IndexController {
    private final PostsService postsService;
    private final HttpSession httpSession;
    @GetMapping("/")
    public String index(Model model) {
        model.addAttribute("posts", postsService.findAllDesc());
        SessionUser user = (SessionUser)httpSession.getAttribute("user");
        if (user != null) {
            model.addAttribute("userName", user.getName());
        }
        return "index";
    }
    @GetMapping("/posts/save")
    public String postsSave() {
        return "posts-save";
    }
}
```
1. 위에서 httpSession에 유저의 정보를 저장했기 때문에 httpSession을 정의합니다.
    - 게시물을 등록하기 위해 PostsService를 등록합니다.
2. user의 정보를 httpSession에서 받아옵니다.
3. 등록한 게시물을 보여주기 위해 postsService의 모든 게시물을 "post"로 model에 속성을 추가합니다.
4. user가 존재한다면 model에 "userName"속성으로 user.getName()을 추가합니다.
   - 이를 통해 HTML에 유저의 이름이 나타납니다.

🥳Naver 로그인 기능을 제외하고 모두 정상적으로 작동함을 알 수 있습니다!

<iframe src="https://giphy.com/embed/kyLYXonQYYfwYDIeZl" width="480" height="360" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/sesamestreet-sesame-street-50th-anniversary-kyLYXonQYYfwYDIeZl">via GIPHY</a></p>

하지만 개발자라면 모름지기 코드 개선을 생각해야 합니다.

### 어노테이션 기반으로 개선
IndexController에서 가장 어색한 부분을 꼽자면 바로 아래와 같은 부분입니다.
```java
SessionUser user = (SessionUser)httpSession.getAttribute("user");
```
만약 다른 라우터에서도 SessoinUser 가 필요하면 위의 코드를 다시 작성해야 합니다. 

🤔그렇다면 위에서 SeesionUser를 정의하면 안되나요?

httpSession에 user attribute가 존재하는지 존재하지 않는지 알 수 없기 때문에 위 방식은 적절하지 않습니다.

그래서 사용자 지정 `어노테이션`을 정의하고 이를 적용하는 방향으로 진행하겠습니다.

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginUser {
}
```

그리고 이 어노테이션을 위한 Resolver(파라미터 가공을 위한)를 작성해보겠습니다.

```java
@RequiredArgsConstructor
@Component
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {
    private final HttpSession httpSession;

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        boolean isLoginUserAnnotation = parameter.getParameterAnnotation(LoginUser.class) != null;
        boolean isUserClass = SessionUser.class.equals(parameter.getParameterType());
        return isLoginUserAnnotation && isUserClass;
    }
    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        return httpSession.getAttribute("user");
    }
}
```
Resolver의 이름은 구현하려는 어노테이션명 + ArgumentResolver 또는 ArgumentResolverSupport로 작성합니다.

또한 `HandlerMethodArgumentResolver`를 implemnets하여 `supportsParameter`, `resolveArgument`를 구현합니다.

supportsParameter를 통해 어노테이션이 존재하는지 검증을 진행하고 resovleArgument를 통해 로직을 진행합니다.

여기에서는 parameter.getParameterAnnotation(LoginUser.class)!=null의 로직을 사용하였고 resolveArgument에서 객체를 전달했습니다.

이렇게 생성한 어노테이션을 프로젝트에서 사용하기 위해 `WebMvcConfigurer`에 추가를 합니다.

### WebMvcConfigurer
```java
@RequiredArgsConstructor
@Configuration
public class WebConfig implements WebMvcConfigurer {
    private final LoginUserArgumentResolver loginUserArgumentResolver;
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
        argumentResolvers.add(loginUserArgumentResolver);
    }
}
```
위와 같이 webconfig를 작성하여 방금 생성한 resovler를 등록합니다.

자 이제 @LoginUser 어노테이션을 사용할 준비가 되었습니다!

```java
public String index(Model model, @LoginUser SessionUser user) {
    model.addAttribute("posts", postsService.findAllDesc());
    if (user != null) {
        model.addAttribute("userName", user.getName());
    }
    return "index";
}
```
위와 같이 어노테이션을 통해 세션정보를 직접 전달하여 차후 세션정보가 필요한 경우에도 쉽게 코드를 작성할 수 있게 유지보수가 완료되었습니다.