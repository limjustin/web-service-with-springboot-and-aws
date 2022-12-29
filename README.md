## Ch 02. 테스트 코드 작성하기

**테스트 코드 개념**

- TDD 실제로 내가 해보기 (”TDD를 이해하고 실천하고 계시는 분” - 배민 모집 공고 中)
- xUnit → 자바에서는 JUnit 이었던 것..!

**코드 파트**

`@RunWith(SpringRunner.class)`

- @SpringBootTest는 ApplicationContext를 전부 로드하기 때문에 테스트 자체가 무거워짐
    
    (→ 모든 빈을 로드하기 때문에 테스트 구동 시간이 오래 걸림)
    
- 필요한 것들(@Autowired, @MockBean)만 ApplicationContext에 로드해준다!
- JUnit4 테스트 라이브러리를 스프링 프레임워크랑 매치해준다고 생각하면 된다!

`@WebMvcTest(Controller.class)`

- ApplicationContext 완전하게 실행시키지 않고 테스트하고 싶을 때
- Controller 부분만 테스트하고 싶을 때 사용할 수 있음!

`private MockMvc mvc`

- 웹 API 테스트
- HTTP GET, POST 등 API 테스트 가능

`mvc.perfrom(get(”/hello”))`

- /hello 주소로 HTTP GET 요청

`.andExpect(status.isOk())`

- isOk( ) : 200 (상태코드)

`.andExpect(content().string(hello))`

- content( ) : 응답에 대한 정보

`.andExpect(jsonPath(”$.name”, is(name)))`

- JSON 응답 값을 검증할 수 있음

<br>

## Ch 03. JPA로 데이터베이스 다뤄보기
**SQL의 필요성**

- 객체를 관계형 데이터베이스에 관리하는 것은 매우 중요
    - 현재 관계형 데이터베이스에 대한 이해도가 없다! 추후에 반드시 공부할 것! 🔥
- 관계형 데이터베이스는 SQL만 인식 가능 → 각 테이블마다 기본적인 CRUD SQL 매번 생성해야
- 따라서 SQL은 피할 수 없다!

**JPA의 등장**

- 패러다임 불일치 문제
- 관계형 데이터베이스에 맞게 SQL을 대신 생성해서 실행

**Spring Data JPA의 등장**

- JPA의 구현체는 Hibernate 등, 하지만 이들을 더 쉽게 사용하고자 추상화시킨 것!!
- JPA ← Hibernate ← Spring Data JPA (한 단계 더 감싸놓은 것, 더 구체적이게 Repository 형태로)
- 저장소를 바꾸고 싶을 때? Spring Data JPA → Spring Data MongoDB 로 의존성만 바꾸면 된다네!
- Spring Data의 하위 프로젝트들은 save(), findAll(), findOne() 등을 인터페이스로 가지고 있기 때문

**도메인**

- 게시글, 댓글, 회원, 정산, 결제 등 소프트웨어에 대한 요구사항 혹은 문제 영역
- Entity 클래스와 기본 Entity Repository는 함께 위치해야 한다!!
- 도메인 별로 프로젝트 분리 필요 시, Entity 클래스와 기본 Repository는 도메인 패키지에 함께 관리하기!!

`@Entity` : 테이블과 링크될 클래스임을 나타냄

`@Id` : 해당 테이블의 Primary Key 필드

`@GeneratedValue(strategy = GenerationType.IDENTITY)` : 기본 키 생성을 데이터베이스에 위임

- 예시 : key 값이 자동으로 카운트 올라가는 로직

`@Column` : 선언하지 않아도 모두 칼럼으로 올라가긴 함, 기본값 외에 추가로 변경이 필요한 옵션이 있을 때 사용

**Builder 패턴**

- 어느 필드에 어떤 값을 채워야할지 명확하게 인지 가능
- 각 인자에 대한 파라미터 주입을 명확하게 할 수 있게 해주는 패턴

```java
public Class Car {
    private String id;
    private String name;

    public Car(String id, String name) {
        this.id = id;
        this.name = name;
    }
}

public class CarImpl {
    private String id = "001";
    private String name = "Ferrari";

    Car car1 = new Car(id, name);  // 파라미터 정확하게 전달되었는지 알 수 없음
    Car car2 = new Car(name, id);
}
```

```java
import lombok;

@Getter @Setter
public Class Car {
    private String id;
    private String name;

    @Builder
    public Car(String id, String name) {
        this.id = id;
        this.name = name;
    }
}

public class CarImpl {
    private String id = "001";
    private String name = "Ferrari";

    Car car3 = Car.builder()
                .id(id). // 각 인자에 대한 파라미터 주입이 명확해졌음
                .name(name)
                .build();
}
```

**Request / Response 클래스의 존재 이유**

- Entity 클래스와 거의 유사한 형태임에도 DTO 클래스를 추가로 생성했음
    
    → 절대로 Entity 클래스를 Request / Response 클래스로 사용해서는 안 된다!!
    
- Entity 클래스는 데이터베이스와 맞닿은 핵심 클래스임
- Request / Response용 DTO는 View를 위한 클래스라 자주 변경이 필요
    
    → 이를 위해 테이블과 연결된 Entity 클래스 변경은 너무 코스트가 크다
    
- View Layer ↔ DB Layer 역할 분리를 철저하게 하는 것이 좋다
