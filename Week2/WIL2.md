필수 정리 내용
* 컨트롤러,서비스,리포지토리의 역할
    * 컨트롤러:웹 MVC의 컨트롤러 역할
    * 서비스: 비즈니스 도메인 객체를 가지고 핵심 로직을 구현하는 계층
    * 리포지토리:데이터 베이스에 접근하고 도메인 객체를 DB에 저장하고 관리한다
* TDD란?왜 하는가?
    *TDD란:Test Drive Development의 약자로 테스트 주도 개발이라고한다. 작은 단위의 테스트 케이스를 작성하고 이를 통과하는 코드를 추가하는 단계를 반복하여 구현한다
    *왜 하는가?: 높은 코드 안정성, 재설계 시간의 단축, 디버깅 시간의 단축같은 장점들이 있다. 하지만 TDD를 쉽게 도입하지 못하는 단점도 존재하긴 한다
    *단점:생산성의 저하이다. 한 번 개발할 코드를 2번 이상 반복하게 되면서 개발 시간이 평소 개발 방식보다 늘어난다.

강의 정리
-회원 도메인과 리포지토리 만들기
* main안에 domain, controller, repository만든다

    repository/memberRepository
    ```java
    package hello.hellospring.repository;
    import hello.hellospring.domain.Member;

    import java.util.Optional;
    import java.util.List;
    public interface MemberRepository{
        Member save(Member member);
        Optional<Member> findById(Long id);
        Optional<Member> findByName(String name);
        List<member> findAll()
    }
    ```
    repository/memorymemberrepository
    ```java
    package hello.hellospring.repository;

    import hello.hellospring.domain.Member;
    import org.springframework.stereotype.Repository;

    import java.util.*;
    public class MemoryMember implements MemberRepository{
        private static Map<Long,Member> store = new Hashmap<>();
        private static long sequence = 0L;

        //부모클래스로부터 상속받은 메소드를 재정의 
        @Override
        public Member save(Member member){
            member.setId(++sequence);
            store.put(member.getId(),member);
            return member
        }

        @Override
        public Optional<Member> findById(Long id){
            return Optional.ofNullable(store.get(id));
        }

        @Override
        public Optional<Member> findByName(String name){
            return store.values().stream().filter(member->member.getName().equals(name));
            .findAny();
        }

         @Override
        public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }
    }
    ```
    이거 작성하면서 궁금해진게 왜 굳이 store을 Hashmap으로 쓴걸까?했는데 성능 때문에 Hashmap을 쓴다한다 시간복잡도측면에서 list보다 훨씬 유리하기 때문이다
    그리고 memberrepository랑 memorymemberrepository쓰면서 느낀건데 express나 nest로 user정보만들고 @Method쓰는 로직이 비슷하다고 느꼈다.. 우매함의 봉우리라서 지금 제대로 말하고 있는지 모르겠는데 아무튼 뭔가 익숙한 로직이라서 반가웠다. JAVA는 그리고 쓸 수 있는 메소드가 다양해서 nest에서 if문을 썻다면 JAVA에서는 그냥 메소드로 해결할 수 있다고 느꼈다

- 회원 리포지토리 테스트 케이스 작성
test폴더 안의 java에 repository폴더를 똑같이 만든다

* repository/MemoryMemberRepository
    ```java
    package hello.hellospring.repository
    import org.junit.jupiter.api.Test;
    class MemoryMemberRepositoryTest{
        MemberRepository repository =new MemoryMemberRepository();

        @Test
        public void save(){
            Member member = new Member();
            member.setName('spring');

            repository.save(member);

            Member result = repository.findById(member.getId()).get();
        }
        assertThat(member).isEqualTo(result);
    }
    @Test
    public void findByName(){
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        Member result= repository.findByName("spring1").get();
        assertThat(result).isEqualTo(member1);
    }
    @Test
    public void findAll(){
        Member member1=new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2=new Member();
        member2.setName("spring2");
        repository.save(member2);

        List<Member> result = repository.findAll();
        assertThat(result.size()).isEqualTo(2);


    }
    ```
    이렇게만 하면 findByName이 에러가 뜨는 이유는?
    findAll()이 실행 되어 spring1과 spring2가 저장이 먼저 되어 findByName()에서 spring1과 spring2가 저장이 안된다
    해결책=>
    MemoryMemberRepository에서
    * ```java
        public void clearStore(){
            store.clear();
        }
       ```
    MemoryMemberRepositoryTest에서
    * ```java
        @AfterEach
        public void afterEach(){
            repository.clearStore();
        }
    ```
-회원 서비스 개발
service폴더를 만들어 MemberService(회원서비스) 패키지를 만든다
회원 serice가 있으려면 MemberRepository를 import해와야 한다

* service/MemberService의 회원가입
    * ```java
        //회원가입
        public Long join(Member member){
            //같은 이름이 있는 경우면 안된다
        validateDuplicateMember(member);

        memberRepository.save(member);
        return member.getId();
        }

        private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m->{
            throw new IllegalStateException("이미 존재하는 회원입니다");
        });
        }
      ```
-회원 서비스 테스트
* 단축키 cmd+shft+t ->create new test가 뜬다
    회원가입()만들때 given,when,then로직으로 짜는 팁!
    *  ```java
        @Test
        void join() {
            //given
            Member member = new Member();
            member.setName("hello");
            //when
            Long saveId =memberService.join(member);
            //then
            Member findMember= memberService.findOne(saveId).get();
            assertThat(member.getName()).isEqualTo(findMember.getName());
        }
        ```
    * try catch 대신 제공하는 assertThrows사용하기!
        * try catch사용시
            ```java
            try{
            memberService.join(member2);
            fail();
            }catch(IllegalStateException e){
            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다");
            }
            ```
        * assertThrows 사용하기
            ```java
            IllegalStateException e = assertThrows(IllegalStateException.class, ()->memberService.join(member2));
             assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다");
            ```
    * MemberServiceTest에서 MemoryMemberRepository를 두번 쓸 필요가 없다! 코드를 개선해보자
        ```java
        MemoryMemberRepository memberRepository = new MemoryMemberRepository();
        ```
        ->테스트할때마다 다른 객체를 생성하지  않으려면 servie/MemberService에 constructor사용!!alt+ins
        ```java
         public MemberService(MemberRepository memberRepository){
         this.memberRepository = memberRepository;}
        ```
        ->MemberServiceTest에다가는 동작하기 전에
        ```java
        @BeforeEach
        public void beforeEach(){
            memberRepository = new MemoryMemberRepository();
            memberService = new MemberService(memberRepository);
        }
        ```
        -->이런것은 dependency injection!!!