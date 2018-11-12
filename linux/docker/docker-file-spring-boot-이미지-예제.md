
## 1. Dokcer-file만으로 이미지 만들기

### Docker 파일생성
```
FROM openjdk:8-jre
COPY target/demo-*.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

### Docker 이미지 생성

``` 
docker build -t test-docker . 
```

### Dokcer 이미지 확인
```
docker images
```

### 도커 이미 파일 실행
```
docker run -p 8080:8080 test-docker .
```

## 2. Maven plugin을 이용해 web 프로젝트 이미지 만들기
위에 단점은 jar를 생성한 후 에 이미지를 만들어야한다는 단점이 있다. maven 플러그인을 하는 순간 이미지를 바로 생성해서 실행할 수 있다.


```xml
            <plugin>
                <groupId>io.fabric8</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>0.27.1</version>
                <configuration>
                    <images>
                        <image>
                            <name>wan/tododemo</name>
                            <build>
                                <dockerFileDir>${basedir}</dockerFileDir>
                            </build>
                        </image>
                    </images>
                </configuration>
                <executions>
                    <execution>
                        <id>docker-build</id>
                        <phase>package</phase>
                        <goals>
                            <goal>build</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```

다음은 ./mvnw clean package 메이븐 빌드를 하자마자 이미지가 생성되어서 실행만  시키면 된다.
```
$ git clone https://github.com/minwan1/todo-web.git
$ cd todo-web
$ ./mvnw clean package
$ docker run -d -p 8080:8080 wan/tododemo

http://localhost:8080/index.html#/
```