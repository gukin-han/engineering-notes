# 빈 생명주기

## 목차

- [빈 생명주기의 단계](#빈-생명주기의-단계)
- [초기화는 생성자와 분리한다](#초기화는-생성자와-분리한다)
- [초기화와 소멸 콜백](#초기화와-소멸-콜백)
- [BeanPostProcessor](#beanpostprocessor)
- [정리](#정리)

---

## 빈 생명주기의 단계

컨테이너는 빈을 만들고 끝내는 게 아니라, 정해진 단계를 거쳐 관리한다.

```
생성(생성자) → 의존성 주입 → 초기화 콜백 → (사용) → 소멸 콜백
```

여기서 중요한 건 **객체 생성과 초기화가 분리된 단계**라는 점이다.
생성자는 객체를 만들기만 하고, 의존성을 활용한 준비 작업은 그 뒤 초기화 단계에서 한다.

---

## 초기화는 생성자와 분리한다

필드 주입이나 세터 주입은 생성자가 실행된 **뒤에** 일어난다.
그래서 생성자 안에서 주입된 의존성을 사용하면 아직 null이다.

```java
@Service
public class NotificationService {

    @Autowired
    private MessageClient messageClient;

    public NotificationService() {
        messageClient.connect(); // NPE: 아직 주입 전이다
    }
}
```

초기화 작업은 `@PostConstruct`로 옮긴다. 모든 의존성 주입이 끝난 뒤 호출된다.

```java
@Service
public class NotificationService {

    private final MessageClient messageClient;

    public NotificationService(MessageClient messageClient) {
        this.messageClient = messageClient;
    }

    @PostConstruct
    public void init() {
        messageClient.connect(); // 주입 완료 후라 안전하다
    }
}
```

생성자 주입을 쓰면 의존성 자체는 생성자에서 받지만, 연결이나 검증 같은 초기화 로직은 여전히 `@PostConstruct`로 분리하는 편이 책임이 명확하다.

---

## 초기화와 소멸 콜백

초기화 콜백은 세 가지 방법이 있다.

* `@PostConstruct` — 표준 애너테이션. 기본으로 사용한다.
* `InitializingBean.afterPropertiesSet()` — 스프링 인터페이스에 묶이므로 지양한다.
* `@Bean(initMethod = "...")` — 코드를 수정할 수 없는 외부 라이브러리 빈에 사용한다.

소멸 콜백도 같은 구조다. `@PreDestroy`를 기본으로 쓰고, 여기서 리소스를 정리한다.

```java
@Service
public class ConnectionPool {

    @PostConstruct
    public void init() {
        // 커넥션을 미리 열어둔다
    }

    @PreDestroy
    public void close() {
        // 컨테이너 종료 시 커넥션을 반환한다
    }
}
```

주의할 점은 **프로토타입 빈은 소멸 콜백이 호출되지 않는다**는 것이다.
컨테이너는 프로토타입 빈을 생성해서 넘겨줄 뿐, 그 이후 생명주기는 관리하지 않는다.
정리가 필요한 리소스를 프로토타입 빈에 두면 누수가 생긴다.

---

## BeanPostProcessor

`BeanPostProcessor`는 **모든 빈의 초기화 전후에 끼어들어 빈을 가공하는 확장점**이다.

```java
@Component
public class LoggingBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) {
        // 초기화가 끝난 빈을 가로채, 그대로 두거나 다른 객체로 바꿔 반환할 수 있다
        return bean;
    }
}
```

이게 중요한 이유는 스프링의 많은 기능이 이 위에서 동작하기 때문이다.

* **AOP**: `postProcessAfterInitialization`에서 원본 빈을 **프록시로 감싸** 반환한다. 우리가 주입받는 건 사실 프록시다.
* `@PostConstruct`, `@PreDestroy` 처리도 `BeanPostProcessor`(`CommonAnnotationBeanPostProcessor`)가 담당한다.

즉 초기화 단계는 단순한 콜백 지점이 아니라, 스프링이 빈을 가로채 기능을 입히는 자리다.
AOP와 트랜잭션이 어떻게 동작하는지는 여기서 출발한다.

---

## 정리

* 빈은 생성 → 의존성 주입 → 초기화 → 소멸 단계를 거친다.
* 객체 생성과 초기화는 분리한다. 의존성을 활용한 준비 작업은 `@PostConstruct`에서 한다.
* 리소스 정리는 `@PreDestroy`에서 한다. 단, 프로토타입 빈은 소멸 콜백이 호출되지 않는다.
* `BeanPostProcessor`는 초기화 전후로 빈을 가공하는 확장점이며, AOP 프록시가 만들어지는 자리다.
