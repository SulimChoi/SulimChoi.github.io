---
layout: post
title: Chapter 2. 웹 애플리케이션 개발하기
category: 스프링 인 액션 (제5판)
permalink: /til/:year/:month/:day/:title/
tags: [스프링 인 액션 (제5판), Spring]
comments: true
---



# 1. 정보 보여주기

- 타코 클라우드는 온라인으로 타코를 주문할 수 있는 애플리케이션
- 타코 클라우드에 풍부한 식자재(ingredient)를 보여주는 팔레트를 사용해 고객이 창의적으로 커스텀 타코를 디자인할 수 있게 하고자 함
  - 고객 자신이 원하는 타코를 디자인할 때 식자재를 보여주고 선택할 수 있는 페이지가 타코 클라우드 웹 애플리케이션에 있어야 함
  - 선택할 수 있는 식자재의 내역은 수시로 변경될 수 있으므로, HTML 페이지에 하드코딩되면 안되며, 사용 가능한 식자재의 내역을 데이터베이스로부터 가져와 고객이 볼 수 있도록 해당 페이지에 전달되어야 함





- 스프링 웹 애플리케이션에서는 데이터를 가져오고 처리하는 것이 컨트롤러의 일이고, 브라우저에 보여주는 데이터를 HTML로 나타내는 것은 뷰가 하는 일
- 타코 디자인 페이지를 지원하기 위해 다음 컴포넌트를 생성
  - 타코 식자재의 속성을 정의하는 도메인(domain) 클래스
  - 식자재 정보를 가져와 뷰에 전달하는 스프링 MVC 컨트롤러 클래스
  - 식자재의 내역을 사용자의 브라우저에 보여주는 뷰 템플릿
- 데이터베이스 관련 내용은 3장으로 미루고, 2장에서는 식자재 내역을 뷰에 제공하는 일만 컨트롤러가 할 것
  - 데이터베이스로부터 식자재를 가져오는 작업은 3장에서 컨트롤러에 추가할 것





### 도메인 설정하기

- 애플리케이션의 도메인은 해당 애플리케이션의 이해에 필요한 개념을 다루는 영역
- 타코 클라우드 애플리케이션의 도메인에는 다음과 같은 객체가 포함됨
  - 고객이 선택한 타코 디자인, 디자인을 구성하는 식자재, 고객, 고객의 타고 주문
  - 우선 타코 식자재에 초점을 둘 것





* 타코 식자재는 매우 간단한 객체
  * 각 식자재는 타입(고기류, 치즈류, 소스류 등)과 이름을 가짐
  * 각 식자재는 쉽고 분명하게 참조할 수 있는 ID를 가짐





* 필요한 도메인 객체인 타코 식자재를 정의하는 Ingredient 클래스

  ```java
  package tacos;
  
  import lombok.Data;
  import lombok.RequiredArgsConstructor;
  
  @Data
  @RequiredArgsConstructor
  public class Ingredient {
  	
  	private final String id;
  	private final String name;
  	private final Type type;
  	
  	public static enum Type {
  		WRAP, PROTEIN, VEGGIES, CHEESE, SAUCE
  	}
  }
  ```

  - 식자재를 나타내는데 필요한 3개의 속성을 정의
  - final 속성들을 초기화하는 생성자와 속성들의 게터(getter)와 세터(setter) 메서드가 없고, `equals()`, `hashCode()`, `toString()` 등의 유용한 메서드도 정의하지 않음
    - 이유: Lombok이라는 라이브러리를 사용해 위의 메서드들을 런타임 시에 자동으로 생성하기 때문
  - 클래스에 `@Data`  애노테이션을 지정하면 소스 코드에 누락된 final 속성들을 초기화하는 생성자와, 속성들의 게터와 세터 등을 생성하라고 Lombok에 알려줌. 따라서 Lombok을 사용하면 Ingredient 클래스의 소스 코드 분량을 줄일 수 있음





- Lombok은 스프링 라이브러리가 아니지만, 이 책의 코드 예제를 간략하게 만들 수 있게 해줌
  - Lombok을 사용하려면 프로젝트에 의존성으로 추가해야 함
  - 의존성을 추가한 후 `@Data`와 같은 Lombok 애노테이션을 코드에 추가하면 컴파일 시에 빌드 명세에 정의한 Lombok이 실행됨
  - 클래스의 속성들을 초기화하는 생성자와, 속성들의 게터와 세터 등을 Lombok이 자동 생성해주므로 에러가 생기지 않음 (개발 시점에는 해당 메서드들이 없다고 STS가 에러로 보여줌)
  - STS의 확장(extension)으로 Lombok을 추가하면 코드 작성 시점에서도 속성 관련 메서드들이 자동 생성되므로 에러가 나타나지 않게 할 수 있음





- Taco 클래스 추가

  ```java
  package tacos;
  
  import java.util.List;
  import lombok.Data;
  
  @Data
  public class Taco {
  	private String name;
  	private List<String> ingredients;
  }
  ```

  - 자바 도메인 객체를 나타내며, 두 개의 속성을 가짐





### 컨트롤러 클래스 생성하기

- 컨트롤러는 스프링 MVC 프레임워크의 중심적인 역할을 수행
  - HTTP 요청을 처리하고, 브라우저에 보여줄 HTML을 뷰에 요청하거나, 또는 REST 형태의 응답 몸체에 직접 데이터를 추가
  - 이 장에서는 웹 브라우저의 콘텐츠를 생성하기 위해 뷰를 사용하는 컨트롤러에 초점을 둘 것 (6장에서 REST API를 처리하는 컨트롤러의 작성 방법을 알아볼 것)





- 타코 클라우드 애플리케이션의 컨트롤러가 수행하는 일
  - 요청 경로가 /design인 HTTP GET 요청을 처리
  - 식자재 내역을 생성
  - 식자재 데이터의 HTML 작성을 뷰 템플릿에 요청하고, 작성된 HTML을 웹 브라우저에 전송





- DesignTacoController 클래스

  ```java
  @Slf4j
  @Controller
  @RequestMapping("/design")
  public class DesignTacoController {
  	
  	@GetMapping
  	public String showDesignForm(Model model) {
  		List<Ingredient> ingredients = Arrays.asList(
  				new Ingredient("FLTO", "Flour Tortilla", Type.WRAP),
  				new Ingredient("COTO", "Corn Tortilla", Type.WRAP ),
  				new Ingredient("GRBF", "Ground Beef", Type.PROTEIN),
  				new Ingredient("CARN", "Carnitas", Type.PROTEIN),
  				new Ingredient("TMTO", "Diced Tomatoes", Type.VEGGIES),
  				new Ingredient("LETC", "Lettuce", Type.VEGGIES),
  				new Ingredient("CHED", "Cheddar", Type.CHEESE),
  				new Ingredient("JACK", "Monterrey Jack", Type.CHEESE),
  				new Ingredient("SLSA", "Salsa", Type.SAUCE),
  				new Ingredient("SRCR", "Sour Cream", Type.SAUCE));
  		
  		Type[] types = Ingredient.Type.values();
  		for (Type type : types) {
  			model.addAttribute(type.toString().toLowerCase(), filterByType(ingredients, type));
  		}
  		
  		model.addAttribute("taco", new Taco());
  		
  		return "design";
  	}
  	
  	private List<Ingredient> filterByType(List<Ingredient> ingredients, Type type){
  		return ingredients.stream().filter(x -> x.getType().equals(type)).collect(Collectors.toList());
  	}
  }
  ```

  - 적용된 애노테이션
    - `@Slf4j`: 컴파일 시에 Lombok에 제공되며, 이 클래스에 자동으로 SLF4K(Simple Logging Facade) Logger를 생성
    - `@Controller`: DesignTacoController 클래스가 컨트롤러로 식별되게 하며, 컴포넌트 검색을 해야 한다는 것을 나타냄. 스프링이 DesignTacoController 클래스를 찾은 후 스프링 애플리케이션 컨텍스트의 빈으로 이 클래스의 인스턴스를 자동 생성함
    - `@RequestMapping`: 이 애노테이션이 클래스 수준으로 적용될 때는 해당 컨트롤러가 처리하는 요청의 종류를 나타냄. 여기서는 DesignTacoController에서 /design으로 시작하는 경로의 요청을 처리함을 나타냄





**GET 요청 처리하기**

- 클래스 수준의 `@RequestMapping`과 함께 사용된 `@GetMapping` 애노테이션은 /design의 HTTP GET 요청이 수신될 때 그 요청을 처리하기 위해 `showDesingForm()` 메서드가 호출됨을 나타냄

- `@GetMapping`은 스프링 4.3에서 소개된 새로운 애놑이션. HTTP GET 요청에 특화되어 있음

- 스프링 MVC에서 사용할 수 있는 요청-대응 애노테이션

  | 애노테이션      | 설명                    |
  | --------------- | ----------------------- |
  | @RequestMapping | 다목적 요청을 처리      |
  | @GetMapping     | HTTP GET 요청을 처리    |
  | @PostMapping    | HTTP POST 요청을 처리   |
  | @PutMapping     | HTTP PUT 요청을 처리    |
  | @DeleteMapping  | HTTP DELETE 요청을 처리 |
  | @PatchMapping   | HTTP PATCH 요청을 처리  |





**`showDesignForm()` 메서드**

- 식자재를 나타내는 Ingredient 객체를 저장하는 List를 생성
  - Ingredient 객체들을 직접 ㅗ드에 추가. 3장에서는 데이터베이스로부터 가져와 저장할 것
- 식자재의 유형(고기, 치즈, 소스 등)을 List에서 필터링(filterByType 메서드)한 후 `showDesignForm()`의 인자로 전달되는 Model 객체의 속성으로 추가
  - Model은 컨트롤러와 데이터를 보여주는 뷰 사이에서 데이터를 운반하는 객체
  - Model 객체의 속성에 있는 데이터는 뷰가 알 수 있는 서블릿(servlet) 요청 속성들로 복사됨
- 제일 마지막에 "design"을 반환
  - 모델 데이터를 브라우저에 나타내는 데 사용될 뷰의 논리적인 이름





- 지금 애플리케이션을 실행하고 브라우저에서 /design 경로에 접속한다면 DesignTacoController의 `showDesignFrom()` 메서드가 실행된 후, 뷰에 요청이 전달되기 전에 List에 저장된 식자재 데이터를 모델 객체에 넣을 것
  - 아직 뷰를 정의하지 않았으므로 HTTP 404 (Not Found) 에러 발생
  - 데이터가 HTML로 작성되어 사용자 웹 브라우저에 나타나게 하는 것이 뷰의 역할





### 뷰 디자인하기

- JSP(JavaServer Pages), Thymeleaf, FreeMarker, Mustache, 그루비 기반의 템플릿 등으로 뷰를 정의할 수 있음
  - 여기서는 Thymeleaf 사용
- Thymeleaf를 사용하려면 프로젝트의 빌드 구성 파일에 또 다른 의존성을 추가해야 함
- Thymeleaf와 같은 뷰 라이브러리들은 어떤 웹 프레임워크와도 사용 가능하도록 설계됨. 따라서 스프링의 추상화 모델을 알지 못하며, 컨트롤러가 데이터를 넣는 Model 대신 서블릿 요청 속성들을 사용
  - 뷰에게 요청을 전달하기 앞서 스프링은 Thymeleaf와 이외의 다른 뷰 템플릿이 사용하는 요청 속성에 모델 데이터를 복사





* Thymeleaf 템플릿은 요청 데이터를 나타내는 요소 속성을 추가로 갖는 HTML

  * ex) key가 "message"인 요청 속성이 있고, Thymeleaf를 사용해 HTML <p> 태그로 나타내고자 했다면

    ```html
    <p th:text="${message}"> placeholder message </p>
    ```

    - `<p>`의 요소의 몸체는 키가 "message"인 서블릿 요청 속성의 값으로 교체됨
    - `th:text`는 교체를 수행하는 Thymeleaf 네임스페이스 속성
    - `${}` 연산자는 요청 속성의 값을 사용하라는 것을 알려줌

  * 또 다른 속성으로 `th:each` 를 제공. 컬렉션을 반복 처리하며, 해당 컬렉션의 요소를 하나씩 HTML로 나타냄. 따라서 List에 저장된 타코 식자재(Ingredient 객체)를 모델 데이터로부터 뷰에 보여줄 때 편리





- Thymeleaf 템플릿 코드
  - 각 유형의 식자재마다 `<div>` 코드가 반복되고 사용자 자신이 생성한 타코의 이름을 지정할 수 있는 필드와 Submit 버튼을 포함
  - Submit yout taco 버튼을 클릭하게 되면 에러(HTTP 405) 발생
    - 이유: DesignTacoController에서 타코를 생성할 준비가 아직 안 되었음





# 2. 폼 제출 처리하기

- 뷰(design.html)의 `<form>` 태그를 보면 method 속성을 보면 POST로 설정되어 있는데 `<form>`에는 action 속성이 선언되지 않은 것을 알 수 있음
  - 폼이 제출되면 브라우저가 폼의 모든 데이터를 모아 폼에 나타난 GET 요청과 같은 경로(/design)로 서버에 HTTP POST 요청을 전송
  - 따라서 이 요청을 처리하는 컨트롤러의 메서드가 있어야 함
- `showDesignForm()` 메서드가 /design 경로의 HTTP GET 요청을 처리하도록 지정하기 위해 `@GetMapping` 애노테이션을 사용
  - POST 요청 처리에는 `@PostMapping` 애노테이션 사용 가능





- 타코 디자인 폼의 제출을 처리하기 위해 `processDesign()` 메서드를 DesignTacoController에 추가

  ```java
  @PostMapping
  public String processDesign(Taco design) {
  	// 이 지점에서 타코 디자인(선택된 식자재 내역)을 저장
  	// 이 작업은 3장에서 할 것
  	log.info("Processing design: " + design);
  		
  	return "redirect:/orders/current";
  }
  ```

  - 클래스 수준의 `@PostMapping`은 `processDesgin()`이 /design 경로의 POST 요청을 처리함을 나타내므로 타코를 디자인하는 사용자가 제출한 것을 여기서 처리
  - 타코 디자인 폼이 제출될 때 이 폼의 필드는 `processDesign()`의 인자로 전달되는 Taco 객체의 속성과 바인딩되므로 `processDesign()` 메서드에서는 Taco 객체를 사용해 어떤 것이든 원하는 처리를 할 수 있음
  - 현재 `processDesign()` 메서드에서 Taco 객체 관련 처리를 아무 것도 하지 않으나, 3장에서 폼으로 제출된 Taco 객체를 데이터베이스에 저장하는 퍼시스턴스 로직을 추가할 것





- checkbox 요소들이 여러 개 있는데, 모두 ingredients라는 이름을 가지며, 텍스트 입력 요소의 이름은 name인 것을 알 수 있음
  - 이 필드들은 Taco 클래스의 ingredients 및  name 속성 값과 바인딩됨
- 폼의 Name 필드는 간단한 텍스트 값을 가질 때만 필요하므로 Taco의 name 속성은 String 타입
- 식자재를 나타내는 checkbox들도 텍스트 값을 갖지만, 0 또는 여러 개가 선택될 수 있으므로, 바인딩되는 Taco 클래스의 ingredients 속성은 선택된 식자재들의 id를 저장하기 위해 `List<String>` 타입이어야 함





- `showDesignFrom()` 메서드와 `processDesign()` 메서드의 차이점
  - `processDesign()`에서 반환되는 값은 리디렉션(redirection, 변경된 경로로 재접속) 뷰를 나타내는 "redirect:"가 제일 앞에 붙음
  - 즉, `processDesign()`의 실행이 끝난 후 사용자의 브라우저가 /orders/current 상대 경로로 재접속되어야 한다는 것을 나타냄





### 타코 주문 폼을 나타내는 컨트롤러

```java
package tacos.web;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import lombok.extern.slf4j.Slf4j;
import tacos.Order;

@Slf4j
@Controller
@RequestMapping("/order")
public class OrderController {
	
	@GetMapping("/current")
	public String orderForm(Model model) {
		model.addAttribute("order", new Order());
		return "orderForm";
	}
}
```

- orders/current 경로의 요청을 처리할 컨트롤러

- `@Slf4j`: 컴파일 시에 SLF4J Logger 객체 생성
  - 생성한 Logger은 제출한 주문의 상세 내역을 로그로 기록하기 위해 사용할 예정
- `orderForm()` 메서드는 orderForm이라는 이름의 뷰를 반환하는 역할을 수행
  - 3장에서 모델 데이터를 데이터베이스에 저장할 때 주문된 Taco 객체들로 모델을 채우도록 이 메서드를 변경할 것





- orderForm 뷰는 orderForm.html이라는 이름의 Thymeleaf 템플릿에 의해 제공됨

  - form 태그에 폼 액션도 지정. 액션이 지정되지 않을 경우에는 폼에 나타났던 것과 같은 URL로 폼의 HTTP POST 요청이 제출되지만, /orders 경로로 제출되도록 지정하고 있음

- /orders 경로의 POST  요청을 처리하는 또 다른 메서드를 OrderController 클래스에 추가해야 함

  ```java
  @PostMapping
  	public String processOrder(Order order) {
  		log.info("Order submitted: " + order);
  		return "redirect:/";
  	}
  ```

  - 주문 데이터의 퍼시스턴스를 처리하는 것은 3장에서 할 것이므로 지금은 간단히 작성
  - 제출된 주문을 처리하기 위해 `processOrder()` 메서드가 호출될 때는 제출된 폼 필드와 바인딩  속성을 갖는 Order 객체가 인자로 전달됨





### 타코 주문 정보를 갖는 도메인 객체

- Order는 주문 정보를 갖는 간단한 클래스

- Order 클래스

  ```java
  package tacos;
  
  import lombok.Data;
  
  @Data
  public class Order {
  	
  	private String deliveryName;
  	private String deliveryStreet;
  	private String deliveryCity;
  	private String deliveryState;
  	private String deliveryZip;
  	private String ccNumber;
  	private String ccExpiration;
  	private String ccCVV;
  }
  ```





# 3. 폼 입력 유효성 검사하기

- 자바의 빈 유효성 검사(Bean Validation) API를 지원
  - 애플리케이션에 추가 코드를 작성하지 않고 유효성 검사 규칙을  쉽게 선언할 수 있음
- 스프링 부트를 사용하면 유효성 검사 라이브러리를 프로젝트에 쉽게 추가할 수 있음
  - 유효성 검사 API와 이 API를 구현한 Hibernate(하이버네이트) 컴포넌트는 스프링 부트의 웹 스타터 의존성으로 자동 추가되기 때문



> **스프링 MVC에 유효성 검사 적용하기**
>
> - 유효성을 검사할 클래스(여기서는 Taco와 Order)에 검사 규칙을 선언
> - 유효성 검사를 해야 하는 컨트롤러 메서드에 검사를 수행한다는 것을 지정
>   - 여기서는 DesignTacoController의 `processDesign()` 메서드와 `OrderController`의 `processOrder()` 메서드가 해당됨
> - 검사 에러를 보여주도록 폼 뷰를 수정



- 유효성 검사 API는 몇 가지 애노테이션을 제공하며, 이 애노테이션들은 검사 규칙을 선언하기 위해 도메인 객체의 속성에 지정할 수 있음
  - 유효성 검사 API를 구현한 Hibernate 컴포넌트에는 더 많은 유효성 검사 애노테이션이 추가됨





### 유효성 검사 규칙 선언하기

- Taco 클래스의 경우는 name 속성의 값이 없거나 null인지 확인하며, 최소한 하나 이상의 식자재 항목을 선택했는지 확인해야 함

  ```java
  package tacos;
  
  import java.util.List;
  
  import javax.validation.constraints.NotNull;
  import javax.validation.constraints.Size;
  
  import lombok.Data;
  
  @Data
  public class Taco {
  	
  	@NotNull
  	@Size(min=5, message="Name must be at least 5 characters long")
  	private String name;
  	
  	@Size(min=1, message="You must choose at least 1 ingredient")
  	private List<String> ingredients;
  }
  ```

  - `@NotNull`과 `@Size`를 사용해 유효성 검사 규칙을 선언





- 제출된 타코 주문의 유효성 검사를 하기 위해서는 Order 클래스에 관련 애노테이션을 적용해야 함

  ```java
  package tacos;
  
  import javax.validation.constraints.Digits;
  import javax.validation.constraints.Pattern;
  import javax.validation.constraints.NotBlank;
  import org.hibernate.validator.constraints.CreditCardNumber;
  
  import lombok.Data;
  
  @Data
  public class Order {
  	
  	@NotBlank(message="Name is required")
  	private String deliveryName;
  	
  	@NotBlank(message="Street is required")
  	private String deliveryStreet;
  	
  	@NotBlank(message="City is required")
  	private String deliveryCity;
  	
  	@NotBlank(message="State is required")
  	private String deliveryState;
  	
  	@NotBlank(message="Zip code is required")
  	private String deliveryZip;
  	
  	@CreditCardNumber(message = "Not a valid credit card number")
  	private String ccNumber;
  	
  	@Pattern(regexp="^(0[1-9]|1[0-2])([\\/])([1-9][0-9])$", message="Must be formatted MM/YY")
  	private String ccExpiration;
  	
  	@Digits(integer=3, fraction=0, message="Invalid CVV")
  	private String ccCVV;
  }
  ```

  - 배달 주소에 관한 속성들의 경우 사용자가 입력을 하지 않은 필드가 있는지 확인만 하면 되므로 자바 빈 유효성 검사 API의 `@NotBlank`  애노테이션을 사용할 것
  - 대금 지불에 관한 필드에서 ccNumber 속성의 경우는 값이 있는지와 입력 값이 유효한 신용 카드 번호인지도 확인해야 함. ccExpiration 속성의 경우는 MM/YY 형식의 값이어야 함. ccCVV 속성의 값은 세 자리 수가 되어야 함. 이런 종류의 유효성 검사를 하기 위해서는 다른 자바 빈 유효성 검사 API 애노테이션과 Hibernate Validator의 또 다른 애노테이션을 사용해야 함
  - 모든 우효성 검사 애노테이션은 message 속성을 가짐. 사용자가 입력한 정보가 애노테이션으로 선언된 유효성 규칙을 충족하지 못할 때 보여줄 메시지를 message 속성에 저장





### 폼과 바인딩될 때 유효성 검사 수행하기

- 각 폼의 POST 요청이 관련 메서드에서 처리될 때 유효성 검사가 수행되도록 컨트롤러를 수정해야 함

- 제출된 Taco의 유효성 검사

  ```java
  @PostMapping
  public String processDesign(@Valid Taco design, Errors errors) {
  	if (errors.hasErrors()) {
  		return "design";
  	}
  		
  	// 이 지점에서 타코 디자인(선택된 식자재 내역)을 저장
  	// 이 작업은 3장에서 할 것
  	log.info("Processing design: " + design);
  		
  	return "redirect:/orders/current";
  }
  ```

  - DesignTacoController의 `processDesign()` 메서드 인자로 전달되는 Taco에 자바 빈 유효성 검사 API의 `@Valid` 애노테이션 추가
    - 제출된 Taco 객체의 유효성 검사를 수행하라고 스프링 MVC에 알려줌
    - 어떤 검사 에러라도 있으면 에러의 상세 내역이 Errors 객체에 저장되어 `processDesign()`으로 전달됨





- 제출된 Order의 유효성 검사

  ```java
  @PostMapping
  public String processOrder(@Valid Order order, Errors errors) {
  	if(errors.hasErrors()) {
  		return "orderForm";
  	}
  		
  	log.info("Order submitted: " + order);
  	return "redirect:/";
  }
  ```

  - OrderController의 `processOrder()` 메서드 변경





### 유효성 검사 에러 보여주기

- Thymeleaf는 `fields`와 `th:errors` 속성을 통해 Errors 객체의 편리한 사용 방법을 제공

- ex) 신용 카드 번호 필드의 유효성 검사 에러를 보여줄 때 에러 참조를 사용하는 `<span>` 요소를 주문 폼 템플릿(orderForm.html)에 추가할 수 있음

  ```html
  <label for="ccNumber">Credit Card #: </label> 
  <input type="text" th:field="*{ccNumber}" /> 
  <span class="validationError"
  	th:if="${#fields.hasErrors('ccNumber')}"
  	th:errors="*{ccNumber}">CC Num Error</span>
  ```

  - `<span>` 요소의 class 속성은 자용자의 주의를 끌기 위한 에러의 명칭을 지정하는데 사용되고, `th:if` 속성에서는 이 `<span>`을 보여줄지 말지를 결정하며, fields 속성의 `hasErrors()` 메서드를 사용해 ccNumber 필드에 에러가 있는지 검사한 후, 있다면 `<span>`이 나타남
  - `th:errors` 속성은 ccNumber 필드를 참조. 필드에 에러가 있다고 가정하고 `<span>`에 사전 지정된 메시지를 검사 에러 메시지로 교체





# 4. 뷰 컨트롤러로 작업하기

- 타코 클라우드 애플리케이션의 세 가지 컨트롤러는 애플리케이션의 서로 다른 기능을 제공하지만 프로그래밍 패턴은 동일
  - 스프링 컴포넌트 검색에서 자동으로 찾은 후 스프링 애플리케이션 컨텍스트의 빈으로 생성되는 컨트롤러 클래스임을 나타내기 위해 모두 `@Controller` 애노테이션을 사용
  - HomeController 외의 다른 컨트롤러에서는 자신이 처리하는 요청 패턴을 정의하기 위해 클래스 수준의 `@RequestMapping` 애노테이션을 사용
  - 메서드에서 어떤 종류의 요청을 처리해야 하는지 나타내기 위해 `@GetMapping` 또는 `@PostMapping` 애노테이션이 지정된 하나 이상의 메서드를 가짐
- HomeController와 같이 모델 데이터나 사용자 입력을 처리하지 않는 단단한 컨트롤러의 경우 다른 방법으로 컨트롤러를 정의할 수 있음





- 뷰에 요청을 전달하는 일만 하는 컨트롤러(**뷰 컨트롤러**)를 선언

  ```java
  package tacos.web;
  
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.ViewControllerRegistration;
  import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
  import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
  
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
  	
  	@Override
  	public void addViewControllers(ViewControllerRegistry registry) {
  		registry.addViewController("/").setViewName("home");
  	}
  }
  ```

  - WebConfig는 뷰 컨트롤러의 역할을 수행하는 구성 클래스. **WebMvcConfigurer 인터페이스를 구현**
  - WebMvcConfigurer 인터페이스는 스프링 MVC를 구성하는 메서드를 정의. 인터페이스임에도 불구하고, 정의된 모든 메서드의 기본적인 구현을 제공하므로 필요한 메서드만 선택해 오버라이딩하면 됨
    - `addViewControllers()`: 하나 이상의 뷰 컨트롤러를 등록하기 위해 사용할 수 있는 ViewControllerRegistry를 인자로 받음
    - 뷰 컨트롤러가 GET 요청을 처리하는 경로인 "/"를 인자로 전달하여 호출
    - ViewControllerRegistration 객체를 반환하고 "/" 경로의 요청이 전달되어야 하는 뷿 home을 지정해 연달아 ViewControllerRegistration 객체의 setViewName()을 호출





# 5. 뷰 템플릿 라이브러리 선택하기

- 스프링 부트 자동-구성에서 지원되는 템플릿

  | 템플릿                 | 스프링 부트 스타터 의존성                           |
  | ---------------------- | --------------------------------------------------- |
  | FreeMarker             | spring-boot-starter-freemarker                      |
  | Groovy 템플릿          | spring-boot-starter-groovy-templates                |
  | JavaServer Pages (JSP) | 없음(톰캣이나 제티 서블릿 컨테이너 자체에서 제공됨) |
  | Mustache               | spring-boot-starter-mustache                        |
  | Thymeleaf              | spring-boot-starter-thymeleaf                       |



- 원하는 뷰 템플릿을 선택하고 의존성으로 추가한 후 /templates 디렉터리에 템플릿을 작성하면, 스프링 부트는 선택한 템플릿 라이브러리를 찾아 스프링 MVC 컨트롤러의 뷰로 사용할 컴포넌트를 자동으로 구성
- JSP를 선택한다면, 내장된 톰캣과 제티 컨테이너를 포함해 자바 서블릿 컨테이너는 /WEB-INF 밑에서 JSP 코드를 찾음. 애플리케이션을 실행 가능한 JAR 파일로 생성한다면 요구사항을 충족시킬 방법이 없으므로 애플리케이션을 WAR 파일로 생성하고 종전의 서블릿 컨테이너에 설치하는 경우에는 JSP를 선택
  - 실행 가능한 JAR 파일로 생성한다면 JSP를 제외한 나머지 중 하나를 선택





### 템플릿 캐싱

- 템플릿은 최초 사용될 때 한 번만 파싱(코드 분석)됨. 파싱된 결과는 향후 사용을 위해 캐시에 저장됨
  - 프로덕션에서 애플리케이션을 실행할 때 매번 요청을 처리할 때마다 불필요하게 템플릿 파싱을 하지 않으므로 성능을 향상시킬 수 있음
  - 개발 시에는 템플릿을 변경한 경우 변경된 웹 브라우저를 보기 위해서는 애플리케이션을 다시 시작해야 함
- 템플릿 캐싱을 비활성화하는 방법이 있음. 각 템플릿의 캐싱 속성만 false로 설정하면 됨
  - 모든 속성은 캐싱을 활성화하는 true가 기본값
  - 만약 false로 변경한 경우, 프로덕션에서 애플리케이션을 배포할 때는 true로 변경해주어야 함
- 스프링 부트의 DevTools를 사용하면 모든 템플릿 라이브러리의 캐싱을 비활성화함
  - 애플리케이션이 실무 운영을 위해 배포될 때는 DevTools 자신이 비활성화되므로 템플릿 캐싱이 활성화될 수 있음





# 요약

1. 스프링은 스프링 MVC라는 웹 프레임워크를 제공하는데, 스프링 MVC는 스프링 애플리케이션의 웹 프론트엔드 개발에 사용
2. 스프링 MVC는 애노테이션을 기반으로 하며, `@RequestMapping`, `@GetMapping`, `@PostMapping`과 같은 애노테이션을 사용해 요청 처리 메서드를 선언할 수 있음
3. 대부분의 요청 처리 메서드들은 마지막에 Thymeleaf 템플릿과 같은 논리 뷰 이름을 반환. 모델 데이터와 함께 해당 요청을 전달하기 위해
4. 스프링 MVC는 자바 빈 유효성 검사 API와 Hibernate Validator 드으이 유효성 검사 API 구현 컴포넌트를 통해 유효성 검사를 지원
5. 모델 데이터가 없거나 처리할 필요가 없는 HTTP GET 요청을 처리할 때는 뷰 컨트롤러를 사용할 수 있음
6. Thymeleaf에 추가하여 스프링은 다양한 뷰 템플릿을 지원



> **Chapter 2 끝!!!**