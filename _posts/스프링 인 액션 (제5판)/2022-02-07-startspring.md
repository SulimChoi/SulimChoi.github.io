---
layout: post
title: Chapter 1. 스프링 시작하기
category: 스프링 인 액션 (제5판)
permalink: /til/:year/:month/:day/:title/
tags: [스프링 인 액션 (제5판), Spring]
comments: true
---



# 1. 스프링이란?

- 대부분의 애플리케이션은 애플리케이션 전체 기능 중 일부를 담당하는 많은 컴포넌트로 구성됨
  - 각 컴포넌트는 다른 애플리케이션 구서 요소와 협력해 작업을 처리
  - 애플리케이션이 실행될 때는 각 컴포넌트가 어떻게든 생성되어야 하고 상호 간에 알 수 있어야 함
- 스프링은 **스프링 애플리케이션 컨텍스트**(Spring application context)라는 **컨테이너**(container)를 제공
  - 이 컨테이너는 컴포넌트들을 생성하고 관리
  - 애플리케이션 컴포넌트 또는 **빈**(bean)들은  스프링 애플리케이션 컨텍스트 내부에서 서로 연결되어 완전한 애플리케이션을 만듦

* 빈의 상호 연결은 **의존성 주입**(Dependency Injection, DI)라고 알려진 패턴을 기반으로 수행됨
  * 애플리케이션 컴포넌트에서 의존(사용)하는 다른 빈의 생성과 관리를 자체적으로 하는 대신 별도의 개체(컨테이너)가 해줌
  * 이 개체에서는 모든 컴포넌트를 생성, 관리하고 해당 컴포넌트를 필요로 하는 빈에 주입(연결)함. 일반적으로 생성자 인자 또는 속성의 접근자 메서드를 통해 처리됨

- 핵심 컨테이너 외에도 스프링과 관련 라이브러리에서는 웹 프레임워크, 다양한 데이터 저장 옵션, 보안 프레임워크, 타 시스템과의 통합, 런타임 모니터링, 마이크로서비스 지원, 리액티브 프로그래밍 모델, 최신 애플리케이션 개발에 필요한 많은 다른  기능을 제공





* 최신 버전의 스프링에서는 자바 기반의 구성(configuration)이 더 많이 사용됨

  ```java
  @Congiguration
  public class ServiceConfiguration {
      @Bean
      public InventoryService inventoryService() {
          return new InventoryService();
      }
      @Bean
      public ProductService productServicd() {
          return new ProductSercive(inventoryService());
      }
  }
  ```

  - `@Configure` 애노테이션은 각 빈을  스프링 애플리케이션 컨텍스트에 제공하는 구성 클래스라는 것을 스프링에게 알려줌
  - 구성 클래스의 메서드에는 `@Bean` 애노테이션이 지정되어 있으며, 각 메서드에서 반환되는 객체가 애플리케이션 컨텍스트의 빈으로 추가되어야 한다는 것을 나타냄
    - 각 빈의 ID가 해당 빈을 정의하는 메서드의 이름과 동일

  > **애노테이션(annotation)**
  >
  > - 클래스, 인터페이스, 함수, 매개변수, 속성, 생성자에 어떤 의미를 추가할 수 있는 기능
  > - 자바 컴파일러가 컴파일 시에 처리
  > - 소스 코드에 추가된 애노테이션 자체는 바이트 코드로 생성되지 않고 주석으로 처리되지만, 애노테이션이 갖는 의미대로 컴파일러가 작업을 수행해줌





* XML 기반 구성에 비해 자바 기반 구성은 더 강화된 타입 안전과 향상된 리팩토링 기능을 포함해 몇 가지 장점을 제공
* 스프링은 자동으로 컴포넌트들을 구성할 수 있는 자동-구성 기능이 있어 별도의 XML 구성이나 자바 구성이 없어도 됨. 따라서 자동-구성을 할 수 없을 경우에만 필요





- 자동-구성은 **자동 연결**(autowiring)과 **컴포넌트 검색**(component scanning) 이라는 스프링 기법을 기반으로 함
  - 컴포넌트 검색을 사용하여 자동으로 애플리케이션의 classpath에 지정된 컴포넌트를 찾은 후 스프링 애플리케이션 컨텍스트의 빈으로 생성할 수 있음
  - 스프링은 자동 연결을 사용하여 의존 관계가 있는 컴포넌트를 자동으로 다른 빈에 주입(연결)함



- **스프링 부트**가 소개되면서 자동-구성 기능이 더욱 향상됨
  - 스프링 부트는 생산성 향상을 제공하는 스프링 프레임워크의 확장
  - 향상된 자동-구성(autoconfiguration) 기능에 의해 환경 변수인 classpath를 기준으로 어떤 컴포넌트가 구성되고 연결되어야 하는지 알 수 있음





# 2. 스프링 애플리케이션 초기 설정하기

- **타코를 주문하는 타코 클라우드 온라인 애플리케이션**을 생성할 것
- 스프링 Initializr(이니셜라이저)를 사용해 애플리케이션을 초기 설정할 것



### 스프링 Initializr(이니셜라이저)

- REST API를 사용하는 브라우저 기반의 웹 애플리케이션
- 원하는 긴으을 구현할 수 있는 스프링 프로젝트의 구조를 생성해줌
- 스프링 Initializr를  사용하는 여러 방법이 있지만 이 책에서는 **Spring Tool Suite(STS)**의 스프링 Initializr 지원 기능을 사용
  - STS는 이클립스를 기반으로 하는 스프링 IDE. 다른 IDE에는 없는 스프링 부트 대시보드(Spring Boot Dashboard) 기능도 제공





### STS를 사용해서 스프링 프로젝트 초기 설정하기

- Packaging에서는 WAR(Web application ARchive)가 아닌 실행 가능한 JAR(Java ARchive) 파일을 선택
  - 기존의 자바 웹 애플리케이션은 WAR 파일로 패키징되며, JAR 파일은 라이브러리와 데스크톱 UI 애플리케이션의 패키징에 사용
  - JAR 패키징은 클라우드를 염두에 둔 선택. WAR 파일은 기존의 자바 애플리케이션 서버에 애플리케이션을 배포할 때는 적합하지만, 대부분의 클라우드 플랫폼에는 잘 맞지 않음
  - 일부 클라우드 플랫폼에서는 WAR 파일을 배포하고 실행할 수 있지만, 모든 자바 클라우드 플랫폼은 실행 가능한 JAR 파일을 사용
  - 만일 기존의 자바 애플리케이션 서버에 애플리케이션을 배포하고자 한다면 WAR 패키징을 선택하고 웹 초기 설정 클래스를 포함시켜야 함





- 프로젝트에 추가할 의존성(dependency) 지정 가능
  - 의존성에는 프로젝트의 애플리케이션에서 사용하는 외부 라이브러리 모듈을 저정
  - 여러 컴포넌트 클래스가 포함된 jar 파일이 주로 사용됨





### 스프링 프로젝트  구조 살펴보기

- `src/main/java`: 애플리케이션 소스 코드
- `src/main/test`: 애플리케이션 테스트 코드
- `src/main/resources`: 자바 리소스가 아닌 것



> **프로젝트 구조에서 알아 둘 주요 항목**
>
> - `mvnw`와 `mvnw.cmd`: 메이븐 래퍼 스크립트. 메이븐이 각자 컴퓨터에  설치되어 있지 않더라도 이 스크립트를 사용해 프로젝트를 빌드할 수 있음
> - `pom.xml`: 메이븐 빌드 명세(프로젝트 빌드 시 필요한 정보)를 지정한 파일
> - `TacoCloudApplication.java`: 스프링 부트 메인 클래스
> - `application.properties`: 처음에는 이 파일의 내용이 없음. 구성 속성 지정할 수 있음
> - `static`: 브라우저에 제공할 정적인 콘텐츠(이미지, 스타일시트, 자바스크립트 등)를 저장할 수 있는 폴더. 처음에는 비어있음.
> - `templates`: 브라우저에 콘텐츠를 보여주는 템플릿 파일을 두는 폴더. 처음에는 비어있지만, 조만간 Thymeleaf 템플릿을 추가할 것
> - `TacoCloudApplicationTests.java`: 스프링 애플리케이션이 성공적으로 로드되는지 확인하는 간단한 테스트 클래스





**빌드 명세 살펴보기 (`pom.xml`)**

- Initializr 폼에서 프로젝트가 메이븐으로 빌드된다는 것을 지정함. 따라서 스프링 Initializr는 지정한 내용들을 갖는 `pom.xml` 파일을 생성
  - 이 파일은 빌드 명세를 정의
- `<parent> 요소의 <version>`: 프로젝트가 부모 POM(Project Object Model) 으로 spring-boot-starter-parent를 갖는다는 것을 지정
  - 이 부모 POM은 스프링 프로젝트에 흔히 사용되는 여러 라이브러리의 의존성 관리를 제공하므로 이런 라이브러리들의 경우 버전을 지정할 필요가 없음
  - 스프링 부트 버전에 의해 정의된 의존성 관리를 계승
- `<dependencies>`: 의존성 정의
  - STS의 프로젝트 위저드에서 선택한 각 의존성이 `<dependencies>` 요소로 지정됨
  - Spring Web, Thymeleaf, Spring Boot DevTools, Lombok은 우리가 선택한 것. spring-boot-stater-test는 테스트를 작성할 것에 대비해 스프링 Initializr가 자동으로 추가해줌
  - Spring Web, Thymeleaf, Test 의존성 항목은 `<artifactId>`에 starter 단어를 포함. 스프링 부트 스타터 의존성을 나타냄. 이 의존성 항목들은 자체적으로 라이브러리 코드를 갖지 않고 다른 라이브러리의 것을 사용
  - 스타터 의존성의 세 가지 장점
    - 필요로 하는 모든 라이브러리의 의존성을 선언하지 않아도 되므로 빌드 파일이 훨씬 더 작아지고 관리하기 쉬워짐
    - 라이브러리 이름이 아닌 기능의 관점으로 의존성을 생각할 수 있음. 만일 웹 애플리케이션을 개발한다면 웹 애플리케이션을 작성할 수 있게 해주는 라이브러리들을 일일이 지정하는 대신 웹 스타터 의존성만 추가하면 됨
    - 라이브러리들의 버전을 걱정하기 않아도 됨. 스프링 부트에 포함되는 라이브러리들의 버전은 호환이 보장되므로 사용하려는 스프링 부트의 버전만 신경 쓰면 됨
- `pom.xml` 파일 마지막에는 스프링 부트 플러그인이 지정됨. 플러그인의 중요 기능은
  - 메이븐을 사용하는 애플리케이션을 실행할 수 있게 해줌
  - 의존성에 지정된 모든 라이브러리가 실행 가능 JAR 파일에 포함되어 있는지, 런타임 시에 classpath에서 찾을 수 있는지 확인
  - 실행 가능 JAR 파일의 메인 클래스로 부트스트랩 클래스를 나타내는 매니페스트 파일을 JAR 파일에 생성함





**애플리케이션의 부트스트랩(구동)**

- 실행 가능 JAR 파일에서 애플리케이션을 실행하므로 제일 먼저 시작되는 부트스트랩 클래스가 있어야 함. 또한, 애플리케이션을 부트스트랩하기 위한 최소한의 스프링 구성도 있어야 함

- 부트스트랩 클래스인 TavoCloudApplication의 내용은 아래와 같음

  ```java
  package tacos;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  
  @SpringBootApplication
  public class TacoCloudApplication {
  
  	public static void main(String[] args) {
  		SpringApplication.run(TacoCloudApplication.class, args);
  	}
  
  }
  ```

  - `@SpringBootApplication`: 이 코드가 스프링 부트 애플리케이션임을 나타냄. 아래 세 개의 애노테이션이 결합한 것
    - `@SpringBootConfiguration`: 현재 클래스를 구성 클래스로 지정. 필요하다면 자바 기반의 스프링 프레임워크 구성을 현재 클래스에 추가할 수 있음
    - `@EnableAutoConfiguration`: 스프링 부트 자동-구성을 활성화. 필요로 하는 컴포넌트들을 자동으로 구성하도록 스프링 부트에 알려줌
    - `@ComponentScan`: 컴포넌트 검색을 활성화함. 스프링은 자동으로 클래스를 찾아 스프링 애플리케이션 컨텍스트에 컴포넌트로 등록
  - `main()` 메서드
    - JAR 파일이 실행될 때 호출되어 실행되는 메서드
    - 실제로 애플리케이션을 시작시키고 스프링 애플리케이션 컨텍스트를 생성하는 SpringApplication 클래스의 `run()` 메서드를 호출
    - `run()` 메서드에 전달되는 두 개의 매개변수는 구성 클래스와 명령행 인자





**애플리케이션 테스트하기**

- 스프링 Initializr는 테스트 클래스를 제공

- 기본적인 테스트 클래스

  ```java
  package tacos;
  
  import org.junit.jupiter.api.Test;
  import org.springframework.boot.test.context.SpringBootTest;
  
  @SpringBootTest
  class TacoCloudApplicationTests {
  
  	@Test
  	void contextLoads() {
  	}
  
  }
  ```

  - 이 클래스에는 하나의 테스트 메서드가 있으며, 실행 코드는 없음
  - 이 테스트 클래스는 스프링 애플리케이션 컨텍스트가 성공적으로 로드될 수 있는지 확인하는 기본적인 검사를 수행
  - 만일 스프링 애플리케이션 컨텍스트의 생성을 방해하는 코드가 있다면 이 테스트가 실패하게 되므로 문제점을 찾아 해결할 수 있음
  - `@SpringBootTest`는 스프링 부트 기능으로 테스트를 시작하라는 것을 JUnit에 알려줌
    - `main()` 메서드의 `SpringApplication.run()` 호출에 부합되는 테스트 클래스를 나타냄
  - 테스트 메서드
    - `@SpringBootTest`가 테스트의 스프링 애플리케이션 컨텍스트를 로드하는 작업을 수행하더라도 테스트 메서드가 없다면 아무 일도 하지 않음
    - `contextLoads()`처럼 실행 코드는 없더라도 테스트 메서드가 있는 경우 `@SpringBootTest` 애노테이션이 작업을 수행하게 되어 스프링 애플리케이션 컨텍스트가 로드됨. 이때 문제가 없는지 테스트하며, 만일 어떤 문제가 생기면 해당 테스트는 실패





# 3. 스프링 애플리케이션 작성하기

- 타코 클라우드 애플리케이션에 추가할 첫 번째 기능은 **홈페이지(hompage)**이며, 다음의 두 가지 코드를 생성
  1. 홈페이지의 웹 요청(request)을 처리하는 컨트롤러(controller) 클래스
  2. 홈페이지의 모습을 정의하는 뷰 템플릿
- 홈페이지를 테스트하는 간단한 테스트 클래스도 작성할 것





### 웹 요청 처리하기

- 스프링은 **스프링 MVC**라고 하는 강력한 웹 프레임워크를 가짐
- 스프링 MVC의 중심에 **컨트롤러**가 있으며, 컨트롤러는 웹 요청과 응답을 처리하는 컴포넌트(또는 구성 요소)
  - 웹 브라우저를 상대하는 애플리케이션의 경우 컨트롤러는 선택적으로 모델 데이터를 채워 응답하며, 브라우저에 반환되는 HTML을 생성하기 위해 해당 응답의 웹 요청을 뷰에 전달
  - 스프링 MVC에 관한 자세한 내용은 2장에서 다룰 것





### 간단한 컨트롤러 클래스 작성

- 루트 경로의 웹 요청을 처리한 후 모델 데이터를 채우지 않고 해당 웹 요청을 홈페이지 뷰에 전달하는 간단한 컨트롤러 클래스 작성

```java
package tacos;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {
	
	@GetMapping("/")
	public String home() {
		return "home";
	}
}
```

- HomeController 클래스는 `@Controller` 애노테이션이 지정되어 있음
  - `@Controller` 는 컴포넌트 검색 시에 HomeController 클래스가 컴포넌트로 식별되게 하는 것이 주 목적
  - 스프링의 컴포넌트 검색에서는 자동으로 HomeController 클래스를 찾은 후 스프링 애플리케이션 컨텍스트의 빈으로 HomeController의 인스턴스를 생성
- `@Component`, `@Service`, `@Repository`를 포함해 소수의 다른 애노테이션들도 `@Controller`와 동일한 기능을 제공하므로 이런 애노테이션들 중 어느 것을 사용해도 됨
  - `@Controller`을 사용한 이유는 애플리케이션에서의 컴포넌트 역할을 더 잘 설명해주기 때문
- `home()` 메서드는 `@GetMapping` 애노테이션으로 지정되어 있음
  - 루트 경로의 /의 HTTP GET 요청이 수신되면 이 메서드가 해당 요청을 처리해야 함
  - 이 코드에서는 home 값을 갖는 String만 반환하고 다른 일은 하지 않음
  - 이 값은 뷰의 논리적인 이름
  - 뷰는 여러 방법으로 구현 가능하지만, Thymeleaf가 classpath에 지정되어 있으므로 여기서는 Thymeleaf를  사용해 뷰 템플릿을 정의할 수 있음





**왜 Thymeleaf를 사용할까?**

- JSP를 스프링 부트와 같이 사용할 때는 몇 가지 고려할 것이 있기 때문 (2장에서 자세히 다룸)
- 논리적인 뷰 이름(여기서는 home) 앞에 /templates/가 붙고 끝에는 .html이 추가된 것이 템플릿 경로와 파일 이름이 됨
  - 여기서는 /templates/home.html
- 이 프로젝트에서는 /src/main/resources/templates/home.html로 템플릿이 저장되어야 함





### 뷰 정의하기

- 타코 클라우드 홈페이지를 정의하는 기본적인 Thymeleaf 템플릿

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="EUC-KR">
        <title>Taco Cloud</title>
    </head>
    
    <body>
        <h1>Welcome to...</h1>
        <img th:src="@{/images/TacoCloud.png}"/>
    </body>
</html> 
```

- `<img>` 태그: 컨텍스트의 상대적인 경로에 위치하는 이미지를 참조하는 `@{...}` 표현식을 사용해 Thymeleaf의 th:src 속성을 지정
- 홈페이지의 웹 요청을 처리하는 컨트롤러와 홈페이지를 보여주는 뷰 템플릿을 갖게됨





### 컨트롤러 테스트하기

- HTML 페이지의 콘텐츠에 대한 어서션(assertion, 원하는 것이 맞는지 테스트하기 위해 지정한 단언)을 설정하기 어렵기 때문에 웹 애플리케이션의 테스트는 까다로울 수 있음
  - 스프링은 웹 애플리케이션을 쉽게 테스트하는 테스트 지원 기능을 제공

- 홈페이지에 적합한 테스트를 작성할 것
  - 루트 경로인 /의 HTTP GET 요청을 수행한 후 성공적인지, 뷰 이름이 home이고 'Welcome to...' 메시지가 포함된 결과가 기대한 대로 나오는지 테스트



```java
package tacos;

import static org.hamcrest.Matchers.containsString;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

@WebMvcTest(HomeController.class)
public class HomeControllerTest {
	
	@Autowired
	private MockMvc mockMvc;
	
	@Test
	public void testHomePage() throws Exception{
		mockMvc.perform(get("/")).andExpect(status().isOk()).andExpect(view().name("home")).andExpect(content().string(containsString("Welcome to...")));
	}
}
```

- HomeControllerTest는 `@StringBootTest` 대신 `@WebMvcTest` 애노테이션을 사용
  - 스프링 부트에서 제공하는 특별한 애노테이션
  - 스프링 MVC 애플리케이션의 형태로 테스트가 실행되도록 함
  - 즉, HomeController가 스프링 MVC에 등록되므로 스프링 MVC에 웹 요청을 보낼 수 있음
- `@WebMvcTest`는  스프링 MVC를 테스트하기 위한 스프링 지원을 설정
  - 이 테스트에서는 실제 서버를 시작하는 대신 스프링 MVC의 모의(mocking) 매커니즘을 사용해도 충분하므로 모의 테스트를 하기 위해 테스트 클래스에 MockMvc 객체를 주입(연결)
- `testHomePage()` 메서드
  - 홈페이지에 대해 수행하고자 하는 테스트를 정의
  - 루트 경로인 /의 HTTP GET 요청을 MockMvc 객체로 수행
  - 기대하는 것(expection)을 다음과 같이 설정
    - 응답은 반드시 HTTP 200 (OK) 상태가 되어야 함
    - 뷰의 이름은 반드시 home이어야 함
    - 브라우저에 보이는 뷰에는 반드시 'Welcome to...' 텍스트가 포함되어야 함
  - MockMvc 객체가 루트 경로인 /의 HTTP GET 요청을 수행한 수, 세 가지 중 어느 하나라도 충족하지 않으면 테스트는 실패

- Run 툴바의 JUnit Test 선택해 테스트 실행
  - 테스트 결과는 패키지 탐색기 창의 탭으로 나타남
  - 테스트가 에러 없이 정상적으로 수행됨





### 애플리케이션 빌드하고 실행하기

- 스프링 부트 대시보드는 IDE 창의 왼쪽 아래에 탭으로 나타남
- 애플리캐이션이 시작되면 작업 진행을 나타내는 로그 항목들이 콘솔에 나타남
  - 그 중 톰캣 애플리케이션 서버를 자동으로 시작시키는 Tomcat started on port(s):8080 (http)라는 로그 항목도 있음
  - 이처럼 작업이 진행되면 만든 홈페이지를 웹 브라우저에서 볼 수 있음
- 톰캣에 애플리케이션을 설치하지 않았는데도 실행 가능한 이유
  - 스프링 부트 애플리케이션에는 실행에 필요한 모든 것이 포함되기 때문에 톰캣과 같은 애플리케이션 서버에 별도로 애플리케이션을 설치할 필요가 없음





### 스프링 부트 DevTools 알아보기

- 개발 시점의 편리한 도구를 제공
  - 코드가 변경될 대 자동으로 애플리케이션을 다시 시작
  - 브라우저로 전송되는 리소스(템플릿, 자바스크립트, 스타일시트...)가 변경될 때 자동으로 브라우저를 새로고침함
  - 템플릿 캐시를 자동으로 비활성화
  - 만일 H2 데이터베이스가 사용 중이라면 자동으로 H2 콘솔을 활성화
- DevTools는 각종 IDE의 플러그인이 아니며, 특정 IDE를 사용해야 하는 것도 아님
- 개발 시에만 사용되도록 했으므로 실제 운영에서는 스스로 비활성화 됨





**자동으로 애플리케이션 다시 시작시키기**

- DevTools는 변경을 감시하며, 뭔가 변경되었음을 알게 되면 자동으로 애플리케이션을 다시 시작시킴
- DevTools를 사용 중일 때 애플리케이션은 JVM에서 두 개의 클래스 로더에 의해 로드됨
  - 하나는 자바 코드, 속성 파일, 프로젝트의 src/main 경로에 있는 모든 것과 함께 로드되고 이것은 자주 변경될 수 있는 것
  - 나머지 클래스 로더는 자주 변경되지 않는 의존성 라이브러리와 함께 로드됨
- 변경이 감지되는 경우 DevTools는 프로젝트 코드를 포함하는 클래스 로더만 다시 로드하고 스프링 애플리케이션 컨텍스트를 다시 시작시킴
  - 다른 클래스 로더와 JVM은 그대로 둬 애플리케이션이 시작하는데 걸리는 시간을 단축해줌
- 단점: 애플리케이션이 자동으로 다시 시작될 때 의존성 변경이 적용될 수 없음
  - 의존성 라이브러리르 포함하는 클래스 로더는 자동으로 다시 로드되지 않기 때문
  - 빌드 명세에 의존성을 추가, 변경, 삭제할 때는 애플리케이션을 새로 시작해야만 변경의 효과가 나타날 수 있음





**자동으로 브라우저를 새로고침하고 템플릿 캐시를 비활성화하기**

- Thymeleaf와 FreeMarker 같은 템플릿에서는 템플릿의 파싱(코드 분석) 결과를 캐시에 저장하고 사용하도록 구성
  - 템플릿이 사용되는 모든 웹 욫ㅇ마다 매번 다시 파싱되지 않게 하기 위해
- 개발 시점에는 템플릿 캐싱이 유용하지 않음. 애플리케이션이 실행 중일 때 템플릿을 변경하고 브라우저르르 새로고침하더라도 변경 전의 캐싱된 템플릿이 사용되므로 변경된 결과를 볼 수 없기 때문. 이 경우 애플리케이션을 다시 시작해야만 변경된 결과를 볼 수 있음
- DevTools는 모든 템플릿 캐싱을 자동으로 비활성화해 문제를 해결. 템플릿을 변경하더라도 브라우저만 새로고침해주면 변경된 템플릿이 적용됨
- DevTools가 사용될 때 애플리케이션과 함께 자동으로 LiveReload 서버를 활성화하면 LiveReload 브라우저 플러그인과 연결될 때는 브라우저에 전달되는 거의 모든 것에 변경이 생길 때 브라우저가 자동으로 새로고침됨





**H2 콘솔**

- 개발용으로 H2 데이터베이스의 사용을 선택하면, 웹 브라우저에서 사용할 수 있는 H2 콘솔도 DevTools가 자동으로 활성화해줌
- 웹 브라우저에서 http://localhost:8080/h2-console에 접속하면 애플리케이션에서 사용하는 데이터를 알 수 있음





### 리뷰하기

> **타코 클라우드 애플리케이션 구축을 위해 지금까지 했던 작업단계**
>
> 1. 스프링 Initializr를 사용해서 프로젝트 초기 구조 생성
> 2. 홈페이지 웹 요청을 처리하기 위해 컨트롤러 클래스 작성
> 3. 홈페이지를 보여주기 위해 뷰 템플릿 정의
> 4. 애플리케이션을 테스트하기 위해 간단한 테스트 클래스 작성



- 빌드 명세를 정의한 `pom.xml` 파일
  - Web과 Thymeleaf 의존성을 선언
  - 스프링의 MVC 프레임워크, 내장된 톰캣, Thymeleaf와 Thymeleaf 레이아웃 dialect 의존성을 포함
- 스프링 부트의 자동-구성 라이브러리도 개입됨
  - 애플리케이션이 시작될 때 스프링 부트 자동-구성에서 의존성 라이브러리들을 감지하고 자동으로 다음 일을 수행
    - 스프링 MVC를 활성화하기 위해 스프링 애플리케이션 컨텍스트에 관련된 빈들을 구성
    - 내장된 톰캣 서버를 스프링 애플리케이션 컨텍스트에 구성
    - Thymeleaf 템플릿을 사용하는 스프링  MVC 뷰를 나타내기 위해 Thymeleaf 뷰 리졸버(resolver)를 구성





# 4. 스프링 살펴보기

### 핵심 스프링 프레임워크

- 스프링에 있는 모든 것의 기반. 핵심 컨테이너와 의존성 주입 프레임워크 외 몇 가지 다른 기능도 제공
- 스프링 MVC 제공
  - 웹 요청을 처리하기 위해 스프링 MVC를 사용해 컨드롤러 클래스를 작성하는 방법은 이미 알아봄
  - HTML이 아닌 출력을 생성하는 REST API를 만들 때도 스프링 MVC를 사용할 수 있음
- 템플릿 기반의 JDBC 지원(JdbcTemplate)을 포함해 기본적인 데이터 퍼시스턴스 지원 제공 (3장에서 자세히)
- 스프링의 가장 최신 번전에서는 리액티브 프로그래밍 지원이 추가됨
  - 스프링 MVC 개념의 스프링 WebFlux라는 새로운 리액티브 웹 프레임워크가 포함됨





### 스프링 부트

- 액추에이터(Actuator)는 애플리케이션의 내부 작동을 런타임 시에 살펴볼 수 있는 기능을 제공하며, 메트릭(metric), 스레드 덤프 정보, 애플리케이션의 상태, 애플리케이션에서 사용할 수 있는 환경 속성이 포함됨
- 환경 속성의 명세 제공
- 핵슴 프레임워크에 추가되는 테스트 지원
- 스프링 부트 CLI(command-line interface, 명령행 인터페이스)를 제공
  - 스프링 부트 CLI를 사용하면 애플리케이션 전체를 그루비 스크립트들로 작성하여 명령행에서 실행할 수 있ㅇㅁ





### 스프링 데이터

- 간단한 자바 인터페이스로 애플리케이션의 데이터 리퍼지터리를 정의할 수 있음
  - 데이터를 저장하고 읽는 메서드를 작명 규칙을 사용해서 정의
- 스프링 데이터는 서로 다른 종류의 데이터베이스와 함께 사용 가능
  - ex) 관계형 데이터베이스인 JPA, 문서형 데이터베이스인 Mongo, 그래프형 데이터베이스인 Neo4j 등





### 스프링 시큐리티

- 인증(authentication), 허가(authorization), API 보안을 포함하는 넓은 범위의 애플리케이션 보안 요구를 다룸

- 4장과 12장에서 다룸





### 스프링 통합과 배치

- 대부분의 애플리케이션은 다른 애플리케이션 또는 같은 애플리케이션의 서로 다른 컴포넌트를 통합(integration)할 필요가 생김
- 이런 요구사항을 해결하기 위해 알려진 몇 가지 애플리케이션 통합 패턴이 존재
  - 스프링 통합과 스프링 배치(batch)는 스프링 기반 애플리케이션의 패턴 구현을 제공
- 스프링 통합: 데이터가 사용 가능한 즉시 처리되는 실시간 통합
- 스프링 배치: 다량의 데이터가 처리되는 시점을 트리거가 알려줄 때 데이터가 수집 처리되는 배치 통합을 처리해줌





### 스프링 클라우드Chapter 1 끝!!!

- 애플리케이션을 하나의 단일체로 개발하는 대신 **마이크로서비스**라는 여러 개의 개별적인 단위들로 합성하는 것
- 스프링을 사용해 클라우드 애플리케이션을 개발하기 위한 프로젝트들의 모음인 스프링 클라우드를 사용





> **요약**
>
> 1. 웹 애플리케이션 생성, 데이터베이스 사용, 애플리케이션 보안, 마이크로서비스 등에서 개발자의 노력을 덜어주는 것이 스프링의 목표
> 2. 스프링 부트는 손쉬운 의존성 관리, 자동-구성, 런타임 시의 애플리케이션 내부 작동 파악을 스프링에서 할 수 있게 함
> 3. 스프링 애플리케이션은 스프링 Initializr를 사용해 초기 설정할 수 있음. 스프링 Initializr는 웹을 기반으로 하며, 대부분의 자바 개발 환경을 지원
> 4. 빈(bean)이라 하는 컴포넌트는 스프링 애플리케이션 컨텍스트에서 자바나 XML로 선언할 수 있으며, 컴포넌트 탐색으로 찾거나 스프링 부트 자동-구성에서 자동으로 구성할 수도 있음





> **Chapter 1 끝!!!**