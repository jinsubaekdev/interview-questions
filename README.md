# interview-questions

소프트웨어 엔지니어 인터뷰에 대한 질문과 답을 적어보려 한다.
설로인(Sirloin)의 깃허브 속 인터뷰 질문 목록이 좋아서 기반 질문들로 삼아서 답할 예정이다.
(출처: https://github.com/sirloin-dev/meatplatform/blob/master/job-description/interview-questions.adoc)

# 항목별 질문 목록

- [Android](#android)
- [Java / JVM](#java--jvm)
- [Kotlin](#kotlin)

## Android

1. 비동기로 처리한 작업을 UI 에 표시하기 위해 어떤 일이 필요한지 설명해주세요.

2. 안드로이드 Activity 처리 결과를 다루는 onActivityResult` 메소드는 왜 deprecated 처리되었을까요?

3. ViewGroup 내에 선언한 View 들에 onClickListener 를 선언할 경우 안드로이드가 이벤트를 어떻게 핸들링하는지 설명해 주시기 바랍니다.

4. systrace 가 뭐고, 결과 분석은 어떻게?

5. Memory leak 을 유발하는 coding pattern?

6. Dagger 를 왜 쓸까요? 다른 대안은 없나요?

7. Android HAL(Hardware Abstraction Layer) 에 대해 설명해주세요.

## Java / JVM

1. JVM 에서의 autoboxing 이란 어떤 현상을 말하는 걸까요?

2. interface default implementation 이란? abstract class 를 상속받는 것과 기본 구현을 들고 있는 interface 를 implements 하는것은 어떤 차이가 있나요?

3. Java stream method 중 map 과 flatMap 의 차이에 대해 설명해주세요.

4. 메소드에서 리스트 타입의 파라미터를 받을 때, ArrayList - List - Collection - Iterable 처럼 구체 타입 뿐 아니라 상위 타입도 받을 수 있습니다. 컬렉션을 받는 어떤 API 를 구현하실 때 구체 타입의 API 디자인을 선호하는지, 추상 타입의 API 디자인을 선호하는지를 설명해 주세요. 왜 그런 선택을 하시나요?

5. Java 의 equals 와 == 의 차이에 대해 설명해주세요. Kotlin 의 == 와 === 는 어떤 차이가 있나요?

6. 스프링의 @Autowired 를 가급적 쓰지 말라는 이야기가 종종 들리는데 원인이 뭘까요?

7. final 키워드를 변수, 메소드, 클래스에 선언하는 것은 어떤 의미가 있습니까?

8. synchronized 를 메소드에 선언하는 것과, 특정 객체에 선언하는 것은 어떤 차이가 있습니까?

9. Reflection 을 유용하게 사용하는 사례를 말씀해 주세요.

10. JDK/JVM 은 대표적으로 OpenJDK 와 Oracle JDK 로 나뉘는데요, 업무에 어떤 JDK 를 사용하시겠습니까? 선택의 이유를 말씀해 주세요.

11. hashCode / equals 메소드의 역할에 대해 아시는 내용을 최대한 설명해주세요.

12. Java 의 Collections.unmodifiableList 같은 API 를 이용해 List 같은 collection 을 변경 불가능하게 만들 수 있습니다. 그렇다면 이 API 를 사용하면 immutability 를 달성할 수 있을까요?

13. 다음 싱글턴 코드의 어떤 점을 개선하실 수 있습니까? (개선이 필요 없을 수도 있음 / 왜?)

```java
class MySingleton {
  private static MySingleton instance;

  public static synchronized MySingleton getInstance() {
    if (instance == null) {
        instance = new MySingleton();
    }
    return instance;
  }
}
```

14. java 9 이상에 도입된 추가 기능들 중 마음에 드는거 아무거나 하나만 설명해주세요.

15. 민감한 정보를 String 으로 저장하는 것과, char[] 또는 StringBuilder/StringBuffer 같은 클래스로 저장하는 것은 어떤 차이가 있나요?

16. 크기를 지정하지 않고 ArrayList 를 new 로 생성하면 크기 10의 ArrayList 가 생성됩니다. Array 는 크기를 넘길 수 없는데 반해 ArrayList 는 꽉 찬 List 에 element 를 추가로 더할 수 있습니다. 그렇다면 10개의 element 를 채워넣은 ArrayList 의 11번째 element 을 add 하기위해 어떤 일이 일어나는지 설명해주세요.

17. java.lang.String 의 hashCode 구현에 대해 고찰해 봅시다. 왜 그런 구현일지, 문제점은 없을지 이야기해주세요.

18. lambda 와 메소드 1개만 있는 익명 클래스 직접 선언은 문법적 차이 외에 어떤 내부적인 차이가 있을까요?

19. Java generics 에는 primitive type 을 쓸 수 없는 문제가 있습니다. 왜 그럴까요? 어떻게 해결할 수 있을까요?

20. I/O 를 Java nio 로 코딩할 때 주의점은 어떤게 있을까요?

21. Java 는 Pure OOP 언어가 아니라고 하는데, 왜 그런 걸까요?

22. java.lang.String 의 length 메소드는 정확한 결과를 반환하지 않는 경우가 종종 있습니다. 정확한 의 의미란 무엇이고, 왜 그럴까요?

23. Maven 이나 Gradle 이, 의존성 선언한 artifact 들을 찾는 과정에 대해 설명해주세요.

24. java.util.Property extends Hashtable, java.util.Stack extends Vector 같은 클래스는 상속으로 망한 대표 사례입니다. 이유를 설명해 주세요.

25. Spring boot 가 stereotype annotation 을 붙인 클래스들을 어떻게 찾고 bean 으로 등록하는지 그 과정을 최대한 상세하게 설명해주세요.

26. Spring 은 @Transactional 어노테이션 붙인 메소드를 어떻게 찾고 트랜잭션을 처리하나요? 그 내부 구현을 상세하게 설명해 주세요.

27. 메소드에 @Transactional 을 붙이는 것과, TransactionTemplate 을 사용해 트랜잭션을 직접 제어하는 것에는 어떤 차이가 있나요? 어떤 방식을 더 선호하시는지 그 이유도 함께 설명해 주시기 바랍니다.

## Kotlin

1. Kotlin 으로 작성한 jvm target 코드는 숨은 비용이 있습니다. 어떤 숨은 비용을 말하는걸까요? 그럼에도 불구하고 Kotlin 을 써야 할까요?

2. Kotlin extension function 이 실제 native code 로 바뀔 때 어떤 형태로 바뀌는지 설명해주세요.
