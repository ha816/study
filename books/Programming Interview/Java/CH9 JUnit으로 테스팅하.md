# JUnit으로 테스팅하기

테스트를 먼저 작성한 후 빌드 환경에 완벽하게 통합된 것을 확인했다면 테스트는 코드를 검사할 때나 출시용 버전을 빌드할 때처럼 빌드가 생성되었을때 항상 문제없이 작동할것이다. 그런데 알 수 없는 이유로 테스트에 문제가 발생한다면 새로 작성한 코드가 문제가 일어 났다는 것이고, 이를 회귀 라고 한다. 

> JUnit 테스트를 통해 얻는 가치는 무엇인가?

JUnit 테스트는 보통 TDD(Test-Driven Development)라는 개발 방법론에 기반을 둔다. 코드가 어떻게 작동하길 바라는지에 관한 예상이나 가정을 기반으로 짧은 실행을 반복하는 테스트를 만드는 절차다.

## JUnit 생명주기

> JUnit 테스트를 실행할때 어떤 일이 일어나는가? 
 
@BeforeClass
: 이 어노테이션이 붙은 메서드는 public static이고 void 타입을 반환해야 한다. 클래스 (객체) 생성 전에 호출되므로 정적 메서드만 처리가 가능하다.

테스트 클래스의 인스턴스 생성. 모든 자바 클래스 처럼 매개변수가 없는 단일 생성자를 선언한다. 

@Before
:  객체 생성이 끝난 후에 이 어노테이션과 void 타입을 반환하는 모든 public 메서드가 실행된다. 각

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE4NjQ5ODU2MiwtMTU0NDkxNTQyNSwtMT
c3NTY1MjczNCwxOTg0OTAyMzUzLDczMDk5ODExNl19
-->