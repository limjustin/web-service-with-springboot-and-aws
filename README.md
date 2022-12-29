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
