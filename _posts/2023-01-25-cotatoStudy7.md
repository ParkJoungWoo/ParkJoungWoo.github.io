---
layout: post
title: "Spring security 1편"
subtitle: 스프링 3주차
categories: spring
tags: [spring]
---
⚠️ 본 게시물은 `스프링 부트와 AWS로 혼자 구현하는 웹서비스`를 참고도서로 활용합니다. 저작권은 본 책의 저서에게 있음을 알립니다.

## 5장 스프링 시큐리티

### 🤔간단히 로그인을 구현해야 한다면 어떻게 하실건가요?

저라면 가장 기본적으로 `DB`에 `id`와 `password`를 저장하고 요청받은 `id`, `password`를 비교해 참/거짓 값을 리턴할 것 같습니다.

하지만 위 방식은 여러 문제를 가지고 있습니다.

1. 예상 가능한 문제는 `DB`에 저장된 비밀번호가 `암호화`되어 있지 않기 때문에 로그인 요청, 회원가입 요청 중간에 탈취가 가능합니다.
2. 로그인 성공 여부를 단순히 참/거짓 값으로 설정한다면 다른 페이지에 접근할 때도 아무런 문제 없이 접근이 가능하게 됩니다. 즉 `인가`에 대한 문제가 발생합니다.

```
인증 : 로그인과 같이 자격을 증명

인가 : 인증이 된 사용자가 자원 접근이 가능하게함(ex : 마이페이지)
```

이러한 문제점을 생각하고 더 나아가 토큰을 적용해보겠습니다.
### 토큰인증방식

> 📌인증 책임을 서버에 두는 세션인증도 있지만 본 내용에서는 토큰인증만 다루겠습니다.

바로 refresh token과 access token을 사용합니다.

1. 사용자는 서버로부터 인증을 하고 refresh token을 발급받습니다.
2. 사용자는 발급받은 refresh token을 통해 access token을 발급받습니다.
3. 사용자는 access token을 통해 인가를 요청합니다.

여기서 refresh token은 유효기간이 길고 사용자의 정보가 암호화 되어있고 유효한 동안 access token을 발급 받을 수 있게 합니다.

access token은 refresh token보다 유효기간이 짧고 이 token을 통해서만 사용자의 자원에 접근할 수 있습니다.

이와 같은 방법이면 token이 유효한지 서버에서 검증하고 암호화도 되어있으며 유효기간이 존재해 일정 시간 후에는 다시 발급받아야 하는 신뢰성도 있습니다.

하지만 위와 같은 방식도 문제가 존재합니다.

<div style="width:100%;height:0;padding-bottom:73%;position:relative;"><iframe src="https://giphy.com/embed/nqtYybzXsCy763Q5EQ" width="100%" height="100%" style="position:absolute" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div><p><a href="https://giphy.com/gifs/nqtYybzXsCy763Q5EQ">via GIPHY</a></p>

*바로 token이 외부에서 탈취가 가능한 점입니다. 세션 인증 방식의 문제인 서버 부하를 해결했지만 외부 탈취에서 자유롭지 않은 점이 토큰 인증 방식의 문제입니다.*

### 😮‍💨이렇게 로그인이 어렵습니다.
이렇게 로그인을 구현하고 안정적인 서비스를 구축하는 것은 까다로운 일입니다.\
하지만 Spring에서 제공하는 프레임워크로 매우 안전한 인증을 간단히 구현 가능합니다.

### 스프링 시큐리티란
Spring security는 `커스터마이징이 가능한 인증, 인가 프레임워크` 입니다.

스프링 시큐리티는 `form-base 인증`과 `OAuth`, `OpenID` 같은 접근 위임 방식 등 여러 방식의 인증을 지원합니다.

또한 스프링 시큐리티는 CSRF 보호를 지원합니다.

> CSRF는 링크 또는 스크립트를 사용하여 사용자가 인증된 대상 사이트로 원하지 않는 HTTP 요청을 전송하는 공격 방식입니다.

### 스프링 시큐리티 적용해보기

<iframe src="https://giphy.com/embed/3ohhwfwxg4d1h82LxS" width="480" height="478" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/cbc-funny-comedy-3ohhwfwxg4d1h82LxS">via GIPHY</a></p>

얼마나 스프링 시큐리티가 좋은지 알겠습니다. 그렇다면 본 책에서 제시하는 예제를 따라해볼까요?

우선 예제를 따라하기 앞서 현재 구현해야하는 서비스와 스프링 시큐리티로 OAuth2를 해야하는 이유를 알아야합니다.

앞서 설명드린 것과 같이 로그인은 구현하는데 많은 노력이 필요하고 보안 이슈 또한 자주 발생합니다. 그러므로 서비스의 규모가 작다면 상대적으로 규모가 크고 신뢰도가 높은 제 3자에게 인증 및 인가를 위임하는 OAuth를 사용합니다.

1. 이 서비스는 유저가 게시물을 등록하는 기능이 있다.
2. 유저는 아이디, 이름, 이메일, 사진으로 구별된다.
3. OAuth를 통해 로그인 과정을 Google, Naver에 위임한다.

이와 같은 배경으로 기능을 어떻게 구현해야 할지 고민해 보겠습니다.

![googlelogin](https://i.stack.imgur.com/QUh9s.png "많이 본 화면")


위와 같이 많이 본 구글 아이디로 로그인 버튼을 누르면 어떤 개인 정보를 제공할지 나타나게 된다.

우리 서비스는 아이디, 이름, 이메일, 사진이 필요하기 때문에 유저의 정보를 저장하기 위한 Entity가 필요하다.

그래서 User Class가 필요하다.

```java
@Getter
@NoArgsConstructor
@Entity
public class User extends BaseTimeEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;
    @Column(nullable = false)
    private String email;
    @Column
    private String picture;
    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private Role role;

    @Builder
    public User(String name, String email, String picture, Role role) {
        this.name = name;
        this.email = email;
        this.picture = picture;
        this.role = role;
    }

    public User update(String name, String picture) {
        this.name = name;
        this.picture = picture;
        return this;
    }
    public String getRoleKey() {
        return this.role.getKey();
    }
}
```

1. 아이디, 이름, 이메일, 사진이 필요해 Column 및 Id 어노테이션을 사용한 점
2. 불필요한 코드를 줄이기 위해 Getter, NoArgsConstructor를 사용한 점
3. User Class대로 매핑이 될 것이기 때문에 Entity 어노테이션을 사용한 점

위와 같은 3가지 이유는 앞에서 배운 내용으로 충분히 이해가 됩니다.

하지만 `Role`은 생뚱맞게 왜 존재하는 걸까요?\
요구사항에 역할에 관련한 기능은 없었는데 말이죠

<div style="width:480px"><iframe allow="fullscreen" frameBorder="0" height="270" src="https://giphy.com/embed/P6trDNTPCVBuH9clMl/video" width="480"></iframe></div>

바로 사용자의 직책(Role)에 따라 권한이 달라지기 때문입니다.

![time](https://support.kmong.com/hc/article_attachments/900002727666/FAQ.PNG "개인정보보관기간")

기능 명세서가 친절하지 않았지만 간단히 회원 가입 약관을 통해 예를 들어보겠습니다.

*회원 탈퇴를 하더라도 내 정보가 바로 사라지는 것이 아닌 삭제처리된 상태로 `개인정보보관기간` 동안 DB에 남아있게 됩니다.*

그렇기 때문에 탈퇴한 회원은 DB에 남아있더라도 게시글을 생성할 수 없어야 합니다.

그래서 역할을 쉽게 관리하기 위해 enum class로 Role을 만들었습니다.

또한 Spring Security의 역할 지정 규칙(ROLE_사용자지정)을 따른 다면 Role을 생성, 관리할 수 있습니다.

```java
@Getter
@RequiredArgsConstructor
public enum Role {
    GUEST("ROLE_GUEST", "손님"),
    USER("ROLE_USER", "일반 사용자");

    private final String key;
    private final String title;
}
```

이와 같이 Role enum class또한 만들었습니다.

> User 클래스의 Role에서 Enumerated(EnumType.STRING) 어노테이션을 붙인 이유는
> 열거형이 어떤 값을 가지는지 단순 숫자로 보는 것 보다 문자열로 확인 하는 것이 
> 더 이해가 쉽기 때문입니다.

이제 서비스의 기본적인 준비가 되었습니다.