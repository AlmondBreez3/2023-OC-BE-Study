필수 정리 내용
* 1.의존과 의존성
    스프링에서의 의존은 객체 간의 의존을 의미한다. 한 클래스가 다른 클래스의 메서드를 실행할 때 이를 의존한다고 표현한다
    이러한 객체들이 서로 의존하는 관계를 의존성이라고 본다
* 2.@Autowired 의존성 주입
    * @Autowired란 스프링 컨테이너에 등록한 빈에게 의존관계주입이 필요할 때 DI을 도와주는 어노테이션
    * 3가지 방법으로 실현이 가능하다
        * 생성자
        * 수정자(setter)
        * 필드
* 3.DIP
    DI는 의존하는 객체를 직접 생성하지 않고 의존 객체를 전달받는 방식을 사용
    Dependency Inversion Principle:고수준 모듈은 저수준 모듈의 구현에 의존해서는 안된다. 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야한다
* 4.스프링 빈과 스프링 컨테이너란?
스프링 컨테이너:ApplicationContext를 스프링 컨테이너라 한다
기존에는 개발자가 AppConfig를 사용해서 직접 객체를 생성하고 DI를 했지만 이제부터는 스프링 컨테이너를 통해서 사용한다
@Configuration이 붙은 AppConfig를 설정하고 @Bean이라는 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다
이렇게 등록된 객체를 스프링 빈이라고 한다
* 5.JDBC와 JPA?
    * ### JDBC

    * ### JPA
        JPA를 사용하면 SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환를 할 수 있다
        스프링 부트와 JPA만 사용해도 개발 생산성이 정말 많이 증가하고 개발해야할 코드도 확연히 줄어든다.

        단순반복하는 코드들이 확연하게 줄어든다
        개발자는 핵심 비즈니스 로직을 개발하는데 집중할 수 있다

```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class MemberController{
	private final MemberService memberService;
	@Autowired
	public MemberController(MemberService memberService){
		this.memberService =memberService;
	}
}
```
->이렇게 하면 에러난다
HOW TO SOLVE?
->각각의 service class위에 @Service, @Repository 코드를 더해준다
->controller를 service와 연결시켜준다 이때 바로 @AutoWired를 쓴다 (dependecy injection)
->service패키지에도 @Autowired추가해준다

* 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때 기본으로 싱글톤으로 등록한다.
	 싱글톤이란 유일하게 하나만 등록해서 공유하는것. 왠만하면 싱글톤 사용한다


* 스프링빈 도입하는 법
	* 컴포넌트 스캔과 자동 의존 관계 설정(위에 나와 있는 내용)
	* 자바코드로 직접 스프링 빈 등록하기(중요! 할 줄알아야 함!)
		* springConfig라는 파일을 만들어
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
			    public MemberService memberService(){
		        return new MemberService(memberRepository());
			    }
			    @Bean
			    public MemberRepository memberRepository(){
			        return new MemoryMemberRepository();
				    }
					}
				```
		* controller는 어쩔 수 없이 @Controller로 도입해줘야 한다

*  옛날에는 setter주입을 했었는데 안좋음, 조립 시점에 생성자로 한번만 주입하고 그 다음에는 변경을 못하게 막는게 
best! 
* 실무에서는 주로 정형화된 컨트롤러,서비스,리포지토리 같은 코드는 컴포넌트 스캔을 사용한다. 그리고 정형화되지 않거나, 상황에 따라
구현 클래스를 변경해야 하면 설정을 통해 스프링 빈으로 등록한다
* 우리가 듣는 강의는 향후 Memory 리포지터리를 다른 리포지토리로 변경할 예정이므로 번거러운 컴포넌트 스캔 방식 대신
자바코드로 스프링 빈을 설정할거다!
* @Autowired @Service같은 것은 스프링 빈이 관리를 하고 있지 않으면 @Autowired같은 것을 사용을 못 한다