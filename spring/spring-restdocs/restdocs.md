## Spring rest docs


### 의존성 추가
```xml
<dependency>
        <groupId>org.springframework.restdocs</groupId>
        <artifactId>spring-restdocs-mockmvc</artifactId>
        <scope>test</scope>
</dependency>
```



```xml
<dependencies>
    ...
    <!-- (1) -->
    <dependency>
        <groupId>org.springframework.restdocs</groupId>
        <artifactId>spring-restdocs-mockmvc</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
<plugins>
    <plugin>
    <!-- (2) -->
        <groupId>org.asciidoctor</groupId>
        <artifactId>asciidoctor-maven-plugin</artifactId>
        <version>1.5.3</version>
        <executions>
            <execution>
                <id>generate-docs</id>
                <phase>prepare-package</phase>
                <goals>
                    <goal>process-asciidoc</goal>
                </goals>
                <configuration>
                    <backend>html</backend>
                    <doctype>book</doctype>
                </configuration>
            </execution>
        </executions>
        <dependencies>
        <!-- (3) -->
            <dependency>
                <groupId>org.springframework.restdocs</groupId>
                <artifactId>spring-restdocs-asciidoctor</artifactId>
                <version>2.0.2.RELEASE</version>
            </dependency>
        </dependencies>
    </plugin>
    <plugin>
    <!-- (4) -->
        <artifactId>maven-resources-plugin</artifactId>
        <version>2.7</version>
        <executions>
            <execution>
                <id>copy-resources</id>
                <phase>prepare-package</phase>
                <goals>
                    <goal>copy-resources</goal>
                </goals>
                <configuration>
                    <outputDirectory>
                        ${project.build.outputDirectory}/static/docs
                    </outputDirectory>
                    <resources>
                        <resource>
                            <directory>
                                ${project.build.directory}/generated-docs
                            </directory>
                        </resource>
                    </resources>
                </configuration>
            </execution>
        </executions>
    </plugin>
</plugins>
```


(1) restdocs 의존성을 추가합니다. scope는 test로 지정합니다.

(2) asciidoctor 플러그인을 추가합니다.

(3) spring-restdocs-asciidoctor 의존성을 추가합니다. 해당 의존성이 추가되면 
snippets 이 자동으로 구성됩니다.

(4) Asciidoctor 플러그인 설정 입니다.
outputDirectory 문서가 출력되는 디렉토리 경로 입니다. 실제 /target/classes/static/docs/ 경로에 문서가 생셩됩니다.


```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberControllerTest {
    @Rule public JUnitRestDocumentation restDocumentation = new JUnitRestDocumentation(); // (1)
    @Autowired private WebApplicationContext context;
    private MockMvc mockMvc; // (2)
    private RestDocumentationResultHandler document;
    // (3)
    @Before
    public void setUp() {
        this.document = document(
                "{class-name}/{method-name}", 
                preprocessResponse(prettyPrint())
        );
        this.mockMvc = MockMvcBuilders.webAppContextSetup(this.context)
                .apply(documentationConfiguration(this.restDocumentation))
                .alwaysDo(document)
                .build();
    }
}
```


* (1) Spring Rest Docs는 JUnit TestNG 등 다양한 테스트 프레임워크를 지원합니다. 본 예제는 JUnit 기반으로 테스트를 진행 하기 때문에 JUnitRestDocumentation 객체를 생성했습니다.
* (2) MockMvc, WebTestClient, Rest Assured 등 다양한 방식으로 Controller에 대한 테스트를 진행 할 수 있습니다. 본 예제는 MockMvc 기반으로 Controller 테스트를 진행하겠습니다.
* (3) RestDocumentationResultHandler 객체와 MockMvc 객체를 생성합니다.
{class-name}/{method-name} 설정은 해당 테스트 클래스의 이름 메서드 이름 기반으로 디렉토리 경로를 설정해서 snippets을 생성합니다. 
    * target/generated-snippets/member-controller-test/get_member에 저장됩니다.
    * preprocessResponse(prettyPrint() 설정을 통해서 해당 문서가 이쁘게 출력됩니다. 
    * 예를들어 JSON Response 값이 JSON 포멧팅에 맞게 출력됩니다.
    * alwaysDo() 메서드로 위에서 생성된 RestDocumentationResultHandler 객체를 의존성 주입해줍니다. 모든 mockMvc 테스트에 대한 snippets이 생성됩니다.
