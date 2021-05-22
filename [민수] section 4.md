# 스프링 빈과 의존관계
* 컴포넌트 스캔과 자동 의존관계 설정
* 자바 코드로 직접 스프링 빈 등록하기


### 빈과 컴포넌트
* 빈(Bean) : Spring IoC 컨테이너 내에서 관리하는 자바 객체
> 1. Spring Container 내에서 생성되는 객체며 애플리케이션의 핵심을 이룬다고 한다. 
> 2. 컨테이너에 의해서 인스턴스화되어 조립되고 관리되는 객체다.
* 컴포넌트(Component) : 구성요소라는 뜻으로 독립적인 단위 모듈
> 1. 시스템에 대한 조작장치라고 생각할 수 있다. 
> 2. @Component 와 같이 사용되는 것은 개발자가 작성한 Class를 bean으로 만드는 것이다.


### 의존관계
##### 회원 컨트롤러에 의존관계 추가 예제
-----------
```java
package hello.hellospring.controller;

import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class MemberController {
  private final MemberService memberService;
  
  @Autowired
  public MemberController(MemberService memberService) {
    this.memberService = memberService;
  }
}
```
---------
* @Controller 도 실제 위치를 찾아가게 되면 @Component를 포함하고 있는 것을 알 수 있다. (추가로, @Service와 @Repository 등등)
* @Autowired가 있으면 스프링이 연관된 객체를 자동으로 찾아서 넣어준다.(물론 스프링 컨테이너 안에 있는 bean이어야 함)
* MemberController 만 @Controller로 bean으로 등록되어 있으므로 Spring Container에서는 MemService의 bean을 찾을 수 없어서 오류!!
* 따라서 spring bean을 등록해주어야 의존관계를 완성시킬 수 있다.


### 컴포넌트스캔
--------
* 컴포넌트 스캔(ComponentScan) : 컴포넌트 스캔이 있는 패키지 내에서의 @Component와 같은 bean 등록을 자동으로 scan하며 등록해주는 역할을 한다.
> 범위가 존재하고 제한할 수 있으나 typesafe 하지 않고 scan의 오류가 생길 수 있으므로 따로 지정하지는 않는다. 
> 컴포넌트 스캔이 있는 패키지 또는 클래스를 시작으로 모든 어노테이션이 부여된 곳을 scan한다.


### 2가지 스프링 빈 등록 방법
1. 위 의존관계 예시처럼 컴포넌트 스캔을 사용하는 방법
2. 자바 코드로 직접 스프링 빈 등록하는 방법


##### 자바코드로 회원 서비스 스프링 빈 등록 예제
--------------
```java
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {
  @Bean
  public MemberService memberService() {
    return new MemberService(memberRepository());
  }
  @Bean
  public MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }
}
```

* 자바코드로 빈 등록을 하는 경우 리포지토리를 다른 리포지토리로 변경할 때 쉽게 변환이 가능!!
* 참고 : DI(Dependency Injection) = 의존관계 주입에는 필드 주입, setter 주입, 생성자 주입이 있다.
* 추가내용 사이트 - https://jhleed.tistory.com/61
> 위 예제는 생성자 주입이며 동작 중 변경할 일이 없는 의존관계에서는 제일 효율적이다.
