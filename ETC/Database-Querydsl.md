## Spring-data-JPA-querydsl
각종 데이터베이스나 검색엔진이 모두 자기만의 데이터 검색을 위한 쿼리 문법을 가지고 있다. 이렇게 다양한 각 데이터 쿼리 솔류션들의 쿼리 문법을 모두 익히는 것은 쉽지 않다. queryDsl은 몇가지 데이터관리 엔진에 대해서 하나의 통일된 문법으로 쿼리를 작성할 수 있게 해준다. 그리고 여기에 더해서 중요한 특징은 쿼리작성을 java 코드로 하며 type safe 하게 작성할 수 있다는 점이다.

~batis를 사용하는 개발자에게는 자바로 쿼리를 작성한다는것이 익숙하지 안음을 넘어서 불쾌하게 느껴지기 까지 할 수 있겠지만 빠른 개발과 쉬운 디버깅을 통해 개발자가 일찍 퇴근할 수 있는것보다  중요한 미덕은 없을것이다.

![](http://i.imgur.com/ftaKwmx.png)

### Type safe(형식 안전성)
자바로 쿼리를 작성하는 criteria도 존재한다. 하지만 queryDsl은 Type safe라는 개념이 존재한다. 기존 솔루션은 쿼리로 문자열을 만든후 orm엔진에 넘기는 방식인데 여기에는 큰문제가 있다. 컴파일 하는과정에서 에러를 발견 할 수 없다는것이다. 또한 만약 스키마가 변경된다면 프로그램 안에서 그부분관 관련된 쿼리 문자열이 모두 수정되어야한다. JPA의 criretia도 필드이름을 stiring 으로 넘기는 방식이라 type safe한 방식은 아니다.

[spring-data-jpainterface-메소드 설명](http://docs.spring.io/spring-data/jpa/docs/1.10.1.RELEASE/reference/html/#jpa.sample-app.finders.strategies)

### 플러그인 추가및 dependency 추가
```xml
<dependency>
	<groupId>com.mysema.querydsl</groupId>
	<artifactId>querydsl-jpa</artifactId>
	<version>3.6.2</version>
</dependency>
	<dependency>
		<groupId>com.mysema.querydsl</groupId>
		<artifactId>querydsl-apt</artifactId>
		<version>3.7.2</version>
		<scope>provided</scope>
	</dependency>


  <plugin>
  <groupId>com.mysema.maven</groupId>
  <artifactId>apt-maven-plugin</artifactId>
  <version>1.1.3</version>
  <executions>
    <execution>
      <goals>
        <goal>process</goal>
      </goals>
      <configuration>
        <outputDirectory>target/generated-sources/java</outputDirectory>
        <processor>com.mysema.query.apt.jpa.JPAAnnotationProcessor</processor>
      </configuration>
    </execution>
  </executions>
</plugin>
```
```
com.querydsl is the root package for Querydsl 4.* and com.mysema.query is the root package for Querydsl 3.*.

The background is that Querydsl was initially owned by Mysema, but is now owned and mantained by the Querydsl team.
```


참고
* [시나몬 브레드](http://adrenal.tistory.com/23)
* [스택오버플러우](https://stackoverflow.com/questions/32469814/the-difference-between-com-mysema-query-and-com-querydsl)










Description	Resource	Path	Location	Type
java.lang.RuntimeException: java.lang.NoClassDefFoundError: com/querydsl/codegen/Keywords (com.mysema.maven:apt-maven-plugin:1.1.3:process:default:generate-sources)

org.apache.maven.plugin.MojoExecutionException: java.lang.RuntimeException: java.lang.NoClassDefFoundError: com/querydsl/codegen/Keywords
	at com.mysema.maven.apt.AbstractProcessorMojo.execute(AbstractProcessorMojo.java:362)
	at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo(DefaultBuildPluginManager.java:134)
	at org.eclipse.m2e.core.internal.embedder.MavenImpl.execute(MavenImpl.java:331)
	at org.eclipse.m2e.core.internal.embedder.MavenImpl$11.call(MavenImpl.java:1362)
	at org.eclipse.m2e.core.internal.embedder.MavenImpl$11.call(MavenImpl.java:1)
	at org.eclipse.m2e.core.internal.embedder.MavenExecutionContext.executeBare(MavenExecutionContext.java:176)
	at org.eclipse.m2e.core.internal.embedder.MavenExecutionContext.execute(MavenExecutionContext.java:112)
	at org.eclipse.m2e.core.internal.embedder.MavenImpl.execute(MavenImpl.java:1360)
	at org.eclipse.m2e.core.project.configurator.MojoExecutionBuildParticipant.build(MojoExecutionBuildParticipant.java:52)
	at org.eclipse.m2e.core.internal.builder.MavenBuilderImpl.build(MavenBuilderImpl.java:137)
	at org.eclipse.m2e.core.internal.builder.MavenBuilder$1.method(MavenBuilder.java:172)
	at org.eclipse.m2e.core.internal.builder.MavenBuilder$1.method(MavenBuilder.java:1)
	at org.eclipse.m2e.core.internal.builder.MavenBuilder$BuildMethod$1$1.call(MavenBuilder.java:115)
	at org.eclipse.m2e.core.internal.embedder.MavenExecutionContext.executeBare(MavenExecutionContext.java:176)
	at org.eclipse.m2e.core.internal.embedder.MavenExecutionContext.execute(MavenExecutionContext.java:112)
	at org.eclipse.m2e.core.internal.builder.MavenBuilder$BuildMethod$1.call(MavenBuilder.java:105)
	at org.eclipse.m2e.core.internal.embedder.MavenExecutionContext.executeBare(MavenExecutionContext.java:176)
	at org.eclipse.m2e.core.internal.embedder.MavenExecutionContext.execute(MavenExecutionContext.java:151)
	at org.eclipse.m2e.core.internal.embedder.MavenExecutionContext.execute(MavenExecutionContext.java:99)
	at org.eclipse.m2e.core.internal.builder.MavenBuilder$BuildMethod.execute(MavenBuilder.java:86)
	at org.eclipse.m2e.core.internal.builder.MavenBuilder.build(MavenBuilder.java:200)
	at org.eclipse.core.internal.events.BuildManager$2.run(BuildManager.java:735)
	at org.eclipse.core.runtime.SafeRunner.run(SafeRunner.java:42)
	at org.eclipse.core.internal.events.BuildManager.basicBuild(BuildManager.java:206)
	at org.eclipse.core.internal.events.BuildManager.basicBuild(BuildManager.java:246)
	at org.eclipse.core.internal.events.BuildManager$1.run(BuildManager.java:301)
	at org.eclipse.core.runtime.SafeRunner.run(SafeRunner.java:42)
	at org.eclipse.core.internal.events.BuildManager.basicBuild(BuildManager.java:304)
	at org.eclipse.core.internal.events.BuildManager.basicBuildLoop(BuildManager.java:360)
	at org.eclipse.core.internal.events.BuildManager.build(BuildManager.java:383)
	at org.eclipse.core.internal.events.AutoBuildJob.doBuild(AutoBuildJob.java:144)
	at org.eclipse.core.internal.events.AutoBuildJob.run(AutoBuildJob.java:235)
	at org.eclipse.core.internal.jobs.Worker.run(Worker.java:55)
Caused by: java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoClassDefFoundError: com/querydsl/codegen/Keywords
	at java.util.concurrent.FutureTask.report(FutureTask.java:122)
	at java.util.concurrent.FutureTask.get(FutureTask.java:192)
	at com.mysema.maven.apt.AbstractProcessorMojo.execute(AbstractProcessorMojo.java:346)
	... 32 more
Caused by: java.lang.RuntimeException: java.lang.NoClassDefFoundError: com/querydsl/codegen/Keywords
	at com.sun.tools.javac.main.Main.compile(Main.java:553)
	at com.sun.tools.javac.api.JavacTaskImpl.doCall(JavacTaskImpl.java:129)
	at com.sun.tools.javac.api.JavacTaskImpl.call(JavacTaskImpl.java:138)
	at com.sun.tools.javac.api.JavacTaskImpl.call(JavacTaskImpl.java:67)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.lang.NoClassDefFoundError: com/querydsl/codegen/Keywords
	at com.querydsl.apt.jpa.JPAConfiguration.<init>(JPAConfiguration.java:54)
	at com.querydsl.apt.jpa.JPAAnnotationProcessor.createConfiguration(JPAAnnotationProcessor.java:42)
	at com.querydsl.apt.AbstractQuerydslProcessor.process(AbstractQuerydslProcessor.java:79)
	at com.sun.tools.javac.processing.JavacProcessingEnvironment.callProcessor(JavacProcessingEnvironment.java:794)
	at com.sun.tools.javac.processing.JavacProcessingEnvironment.discoverAndRunProcs(JavacProcessingEnvironment.java:705)
	at com.sun.tools.javac.processing.JavacProcessingEnvironment.access$1800(JavacProcessingEnvironment.java:91)
	at com.sun.tools.javac.processing.JavacProcessingEnvironment$Round.run(JavacProcessingEnvironment.java:1035)
	at com.sun.tools.javac.processing.JavacProcessingEnvironment.doProcessing(JavacProcessingEnvironment.java:1176)
	at com.sun.tools.javac.main.JavaCompiler.processAnnotations(JavaCompiler.java:1170)
	at com.sun.tools.javac.main.JavaCompiler.compile(JavaCompiler.java:856)
	at com.sun.tools.javac.main.Main.compile(Main.java:523)
	... 7 more
Caused by: java.lang.ClassNotFoundException: com.querydsl.codegen.Keywords
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 18 more
	pom.xml	/springboot	line 224	Maven Build Problem
