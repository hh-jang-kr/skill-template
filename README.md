# 🚀 Redis + Spring WebFlux 반응형 프로그래밍 학습

## ⭐ 핵심 학습 내용
> [!IMPORTANT]  
> 1. **Spring WebFlux & Project Reactor**
>    - 비동기 논블로킹 프로그래밍 패러다임 이해
>    - Mono/Flux를 활용한 반응형 프로그래밍
> 2. **Redis**
>    - Pub/Sub 기능을 활용한 실시간 데이터 처리
>    - 캐싱 전략 구현
> 3. **R2DBC**
>    - 반응형 데이터베이스 연동 학습

## 💡 학습 동기
- MSA 환경에서 필수적인 비동기 처리 기술 습득
- 실시간 데이터 처리 역량 강화
- 새로운 프로그래밍 패러다임 이해
- Spring 생태계의 최신 기술 스택 학습

## 🎯 주요 학습 포인트

### 1. WebFlux & Reactor 핵심 개념
```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    // 비동기 데이터 처리 예제
    public Flux<UserDTO> getActiveUsers() {
        return userRepository.findAll()
            .filter(user -> user.isActive())
            .map(this::toDTO)
            .doOnNext(dto -> log.info("Processing user: {}", dto.getId()))
            .onErrorResume(e -> {
                log.error("Error processing users", e);
                return Flux.empty();
            });
    }
    
    // 백프레셔 처리 예제
    public Flux<UserDTO> getUsersWithBackpressure() {
        return userRepository.findAll()
            .limitRate(100)
            .sample(Duration.ofSeconds(1))
            .map(this::toDTO);
    }
}
```

### 2. Redis 활용 학습
```java
@Component
public class RedisPubSubExample {
    private final ReactiveRedisTemplate<String, String> redisTemplate;
    
    // Redis Pub/Sub 구현 예제
    public Flux<String> subscribeToChannel(String channel) {
        return redisTemplate.listenToChannel(channel)
            .map(ReactiveSubscription.Message::getMessage)
            .doOnSubscribe(s -> log.info("Subscribed to channel: {}", channel))
            .doOnError(e -> log.error("Subscription error", e));
    }
}
```

### 3. 개념 적용 사례
```java
@RestController
@RequiredArgsConstructor
public class NotificationController {
    private final NotificationService notificationService;

    @GetMapping(value = "/notifications/stream", 
                produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<NotificationDTO> streamNotifications() {
        return notificationService.getNotificationStream()
            .map(NotificationDTO::from)
            .doOnCancel(() -> log.info("Client cancelled the stream"));
    }
}
```

## 📖 학습한 내용 정리

### 1. 반응형 프로그래밍 이해
- Publisher/Subscriber 패턴
- Cold/Hot Sequence 차이점
- 백프레셔 처리 방법
- 에러 처리 전략

### 2. 실제 구현 경험
- SSE(Server-Sent Events) 구현
- 비동기 데이터 스트림 처리
- 반응형 Redis 연동
- 예외 처리 패턴

### 3. 새롭게 알게된 점
- 명령형 vs 선언형 프로그래밍 차이
- 비동기 프로그래밍의 장단점
- 디버깅 전략과 도구들

## 🔍 어려웠던 점과 해결 과정

### 1. 비동기 디버깅의 어려움
```java
// 디버깅을 위한 로그 추가
Flux.from(publisher)
    .doOnNext(value -> log.debug("Value: {}", value))
    .doOnError(e -> log.error("Error: ", e))
    .doOnComplete(() -> log.debug("Completed"))
    .subscribe();
```

### 2. 복잡한 비즈니스 로직 처리
```java
// 체이닝을 통한 가독성 개선
return userRepository.findById(userId)
    .flatMap(user -> enrichUserData(user))
    .flatMap(user -> validateUser(user))
    .flatMap(user -> saveUser(user))
    .doOnSuccess(user -> sendNotification(user));
```

## 📚 학습 자료 정리
- [Spring WebFlux 공식 문서](https://docs.spring.io/spring-framework/reference/web/webflux.html)
- [Project Reactor Reference](https://projectreactor.io/docs/core/release/reference/)
- [Redis 공식 문서](https://redis.io/docs)
- [R2DBC 공식 문서](https://r2dbc.io/)

## 🎓 다음 학습 계획
1. Kotlin Coroutines 학습
2. WebFlux Security 적용
3. 테스트 코드 작성 방법 심화
4. 운영 환경 모니터링 방법

## 💻 로컬 실행 방법
```bash
# Redis 실행
docker run --name redis -p 6379:6379 -d redis

# 애플리케이션 실행
./gradlew bootRun

# 테스트 실행
./gradlew test
```

## 🤝 피드백 & 연락처
- 이메일: example@email.com
- 블로그: https://tech-blog.example.com
- GitHub: https://github.com/example
