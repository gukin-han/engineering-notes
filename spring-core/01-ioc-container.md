# IoC 컨테이너와 DI

## 목차

- [IoC 컨테이너가 필요한 이유](#ioc-컨테이너가-필요한-이유)
- [DI 방식](#di-방식)
- [생성자 주입 코드 줄이기](#생성자-주입-코드-줄이기)
- [같은 타입 빈이 여러 개일 때](#같은-타입-빈이-여러-개일-때)
- [순환 참조](#순환-참조)
- [BeanFactory와 ApplicationContext](#beanfactory와-applicationcontext)
- [빈 생성 시점](#빈-생성-시점)
- [싱글톤 빈과 멀티스레드](#싱글톤-빈과-멀티스레드)
- [정리](#정리)

---

## IoC 컨테이너가 필요한 이유

애플리케이션은 여러 객체가 서로 의존하면서 동작한다.
문제는 객체가 직접 의존 객체를 생성하면 특정 구현체에 강하게 묶인다는 점이다.

```java
private final MemberRepository repository = new JdbcMemberRepository();
```

이렇게 작성하면 구현체를 바꾸거나 테스트용 객체로 대체하기 어렵다.

IoC 컨테이너는 객체의 생성, 의존성 연결, 생명주기 관리를 애플리케이션 코드 대신 담당한다.
객체는 필요한 의존성을 직접 만들지 않고 외부에서 주입받는다. 이것이 DI다.

핵심은 객체 생성을 스프링에 맡기는 것이 아니라, **객체 간 결합을 낮추고 조립 방식을 외부로 분리하는 것**이다.

---

## DI 방식

스프링의 대표적인 의존성 주입 방식은 세 가지다.

* 생성자 주입
* 세터 주입
* 필드 주입

실무에서는 생성자 주입을 기본으로 사용한다.

```java
@Service
public class MemberService {

    private final MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

생성자 주입의 장점은 명확하다.

* 필수 의존성이 생성자에 드러난다.
* `final`을 사용할 수 있어 의존성을 불변으로 유지할 수 있다.
* 객체가 불완전한 상태로 생성되는 것을 막을 수 있다.
* 스프링 없이도 테스트 객체를 직접 만들기 쉽다.
* 순환 참조 같은 설계 문제를 초기에 발견하기 쉽다.

세터 주입은 선택적 의존성이 있을 때 사용할 수 있다.
필드 주입은 의존성이 숨겨지고, 불변성을 보장하기 어려우며, 테스트도 불편하므로 지양한다.

---

## 생성자 주입 코드 줄이기

생성자가 하나뿐이면 `@Autowired`를 생략해도 스프링이 그 생성자로 주입한다.

여기에 Lombok `@RequiredArgsConstructor`를 쓰면 `final` 필드를 받는 생성자를 자동으로 만들어 준다.

```java
@Service
@RequiredArgsConstructor
public class MemberService {

    private final MemberRepository memberRepository;
}
```

앞에서 직접 작성한 생성자와 동작은 같다. 의존성이 늘어도 생성자를 손볼 필요가 없어, 실무에서는 이 방식을 표준으로 쓴다.

---

## 같은 타입 빈이 여러 개일 때

인터페이스 구현체가 둘 이상이면 스프링은 어떤 빈을 주입할지 알 수 없어 `NoUniqueBeanDefinitionException`을 던진다.

```java
public interface PaymentClient {}

@Component
public class TossPaymentClient implements PaymentClient {}

@Component
public class KakaoPaymentClient implements PaymentClient {}
```

해결 방법은 상황에 따라 나뉜다.

하나를 기본값으로 정하려면 `@Primary`를 붙인다.

```java
@Component
@Primary
public class TossPaymentClient implements PaymentClient {}
```

특정 빈을 콕 집으려면 `@Qualifier`로 빈 이름을 지정한다.

```java
public OrderService(@Qualifier("kakaoPaymentClient") PaymentClient paymentClient) {
    this.paymentClient = paymentClient;
}
```

구현체를 전부 받고 싶다면 `List`나 `Map`으로 주입한다. `Map`의 키는 빈 이름이 된다.

```java
@Service
public class PaymentService {

    private final List<PaymentClient> clients;

    public PaymentService(List<PaymentClient> clients) {
        this.clients = clients;
    }
}
```

결제 수단이나 알림 채널처럼 종류별 구현이 늘어나는 경우, 이 방식이면 `if` 분기 없이 구현체를 추가하는 것만으로 확장된다. 전략 패턴을 스프링이 대신 조립해 주는 셈이다.

---

## 순환 참조

A가 B를 필요로 하고 B가 다시 A를 필요로 하면 순환 참조가 된다.

생성자 주입에서는 서로 상대가 먼저 완성되어야 생성할 수 있으므로, 애플리케이션이 기동 시점에 실패한다. 스프링 부트 2.6부터는 순환 참조를 기본적으로 막는다.

```java
@Service
@RequiredArgsConstructor
public class OrderService {
    private final MemberService memberService;
}

@Service
@RequiredArgsConstructor
public class MemberService {
    private final OrderService orderService;
}
```

순환 참조는 대부분 **설계 신호**다. 두 서비스가 같은 책임을 나눠 들고 있다는 뜻이므로, 공통 로직을 제3의 빈으로 분리하면 순환이 자연스럽게 풀린다.

구조를 당장 바꾸기 어렵다면 우회 수단이 있다.

* `@Lazy`로 한쪽 주입을 지연시킨다. 프록시를 먼저 주입받고, 실제 호출 시점에 빈을 가져온다.
* 직접 의존 대신 이벤트(`ApplicationEventPublisher`)로 느슨하게 연결한다.

다만 이건 마지막 수단이다. 순환이 보이면 우회보다 설계를 먼저 의심한다.

---

## BeanFactory와 ApplicationContext

`BeanFactory`는 빈을 생성하고 의존성을 주입하는 스프링 컨테이너의 기본 기능을 제공한다.

`ApplicationContext`는 `BeanFactory`를 확장한 컨테이너다.
빈 관리 기능에 더해 이벤트, 메시지 국제화, 환경 설정, AOP, 리소스 로딩 같은 애플리케이션 기능을 제공한다.

스프링 부트 애플리케이션에서는 대부분 `ApplicationContext`를 사용한다.

---

## 빈 생성 시점

스프링의 기본 빈 스코프는 싱글톤이다.
싱글톤 빈은 애플리케이션 컨텍스트가 시작될 때 미리 생성된다.

```java
@Service
public class OrderService {
}
```

이 빈은 컨테이너 안에 하나만 생성되고, 여러 곳에서 같은 인스턴스를 공유한다.

반면 프로토타입 빈은 컨테이너에 요청할 때마다 새로 생성된다.

```java
@Scope("prototype")
@Component
public class OrderValidator {
}
```

빈 생성을 늦추고 싶다면 `@Lazy`를 사용할 수 있다.

```java
@Lazy
@Service
public class HeavyService {
}
```

다만 `@Lazy`를 사용하면 실제 사용 시점에 초기화 비용이나 오류가 발생할 수 있다.
따라서 꼭 필요한 경우에만 제한적으로 사용하는 편이 좋다.

---

## 싱글톤 빈과 멀티스레드

스프링의 싱글톤 빈은 하나의 인스턴스를 여러 요청 스레드가 공유한다.
따라서 싱글톤 빈이라고 해서 자동으로 스레드 안전한 것은 아니다.

안전하게 사용하려면 기본적으로 무상태로 설계해야 한다.

```java
@Service
public class PriceService {

    public int calculatePrice(int quantity, int unitPrice) {
        return quantity * unitPrice;
    }
}
```

이 코드는 요청별 데이터가 메서드 파라미터와 지역 변수 안에서만 사용되므로 안전하다.

반대로 인스턴스 필드에 요청별 상태를 저장하면 위험하다.

```java
@Service
public class PriceService {

    private int lastCalculatedPrice;

    public int calculatePrice(int quantity, int unitPrice) {
        this.lastCalculatedPrice = quantity * unitPrice;
        return this.lastCalculatedPrice;
    }
}
```

`lastCalculatedPrice`는 여러 스레드가 공유하는 값이다.
동시에 여러 요청이 들어오면 한 요청의 값이 다른 요청에 영향을 줄 수 있다.

따라서 서비스 빈에는 요청별 상태를 저장하지 않는다.
필요한 값은 지역 변수, 파라미터, DB, Redis, 세션처럼 명확한 저장 위치를 사용한다.

---

## 정리

IoC 컨테이너는 객체 생성과 의존성 연결을 애플리케이션 코드에서 분리한다.
DI는 객체가 필요한 의존성을 외부에서 주입받도록 만드는 방식이다.

생성자 주입은 필수 의존성을 명확히 드러내고, 불변성을 보장하며, 테스트하기 쉬운 구조를 만든다.

같은 타입 빈이 여러 개면 `@Primary`나 `@Qualifier`로 고르고, 전부 필요하면 `List`나 `Map`으로 주입한다.
순환 참조는 대부분 설계 신호이므로, 책임을 분리해 끊는 것을 먼저 고려한다.

스프링의 기본 빈은 싱글톤이므로 여러 스레드가 하나의 인스턴스를 공유한다.
따라서 서비스 빈은 가능한 무상태로 설계해야 한다.
