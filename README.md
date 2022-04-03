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

   ## 어플리케이션의 실행

   안드로이드 프레임워크는 리눅스 기반으로 되어있습니다. 따라서 우리가 어플리케이션을 실행시킬 때 안드로이드 시스템은 해당 어플리케이션을 위한 리눅스 프로세스를 생성합니다. 뿐만 아니라 어플리케이션의 실행을 위해 쓰레드를 하나 생성하는데 이는 _메인 쓰레드_ 또는 *UI 쓰레드*라고 불리게 됩니다.

   ## 메인 쓰레드

   메인 쓰레드는 핸들러 쓰레드입니다. 다른 핸들러 쓰레드들과 같은 형태로 구현이 되어있지만 UI에 대한 처리를 할 수 있는 유일한 쓰레드라는 특별함을 가지고 있습니다. 메인 쓰레드는 입력 이벤트 처리나 생명 주기에 연관된 콜백 함수를 처리하는 등 눈에 보이는 UI 및 사용자와의 상호작용에 관련된 작업들을 처리하는 역할을 가지고 있습니다.

   ## 메인 쓰레드의 핵심 동작들이 멈춘다면

   시간이 오래 걸리는 작업은 분리된 다른 쓰레드에서 실행되어야 합니다. 네트워크 작업 또는 데이터베이스 호출 등이 대표적으로 시간이 오래 걸리는 작업들입니다. 위와 같은 작업들은 동기적으로 동작합니다. 만약 이러한 작업들이 메인 쓰레드에서 실행된다면 작업이 완료되기 전까지 메인 쓰레드가 해야할 중요한 작업들을 막게 됩니다. 그 결과 메인 쓰레드는 UI를 지속적으로 업데이트하지 못하게 되고 사용자의 입력을 받지 못하면서 사용자는 마치 앱이 동작하지 않는 것처럼 느끼게 됩니다. 이러한 현상은 안좋은 사용자 경험을 제공하게 되며, 메인 쓰레드가 오랜 시간 멈춰있을 경우 ANR(Application Not Responding) 오류가 발생하며 어플리케이션이 종료될 수 있습니다.

   ## 쓰레드

   쓰레드는 프로그램이 실행되면서 사용되는 실행의 흐름입니다. 자바 가상 머신은 동시에 동작할 수 있는 여러개의 쓰레드를 가지는 것을 허용해줍니다. 안드로이드는 메인 쓰레드를 중심으로 동작하는 단일 쓰레드 모델이기 때문에 작업을 수행하기 위한 다른 쓰레드들을 만들어서 수행 결과를 UI를 업데이트 할 수 있는 메인 쓰레드로 보내줘야 합니다.

   쓰레드를 만들기 위한 두가지 방법이 있습니다.

   ### 1. Thread 클래스를 확장하여 만들기

   ```kotlin
    class MyOwnThread : Thread() {
        override fun run() {
            super.run()
            Log.d("쓰레드", "Thread를 확장하여 생성. 현재 쓰레드 -> ${Thread.currentThread().name}")
        }
    }

    class MainActivity : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)

            val button: Button = findViewById(R.id.button)
            button.setOnClickListener {
                val myOwnThread = MyOwnThread()
                myOwnThread.start()
            }
        }
    }
   ```

   ```
   버튼 클릭 결과: D/쓰레드: Thread를 확장하여 생성. 현재 쓰레드 -> Thread-2
   ```

   ### 2. Runnable 인터페이스를 구현하여 만들기

   ```kotlin
    class MyOwnRunnable : Runnable {
        override fun run() {
            Log.d("쓰레드", "Runnable을 구현하여 생성. 현재 쓰레드 -> ${Thread.currentThread().name}")
        }
    }

    class MainActivity : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)

            val button: Button = findViewById(R.id.button)
            button.setOnClickListener {
                val myOwnRunnable = MyOwnRunnable()
                val newThread = Thread(myOwnRunnable)
                newThread.start()
            }
        }
    }
   ```

   ```
   버튼 클릭 결과: D/쓰레드: Runnable을 구현하여 생성. 현재 쓰레드 -> Thread-2
   ```

   💡 Runnable 인터페이스는 쓰레드와 함께 실행하기 위해 사용된다. Runabble 인터페이스를 구현할 때 run() 메서드 안에 실행할 내용을 작성하면 쓰레드가 시작할 때 해당 내용을 실행하게 된다.

   ## 메인 쓰레드로 수행 내용 전달

   별도의 쓰레드에서 작업한 내용으로 UI를 업데이트 하고 싶다면, 핸들러 또는 runOnUIThread 메서드를 사용해야 한다. 현재 쓰레드가 메인 쓰레드라면 수행 내용은 즉시 실행된다. 만약 현재 쓰레드가 메인 쓰레드가 아니라면 수행 내용은 메인 쓰레드의 이벤트 큐로 전달된다.

   ```kt
   // runOnUIThread 메서드 사용 예시

   class MainActivity : AppCompatActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)

           val button: Button = findViewById(R.id.button)
           button.setOnClickListener {
               Thread(object : Runnable {
                   override fun run() {
                       Log.d("쓰레드", "쓰레드 시작! 1초 뒤 버튼 무효화 시킬 예정")
                       Thread.sleep(1000)
                       runOnUiThread(object: Runnable {
                           override fun run() {
                               button.text = "유효하지 않은 버튼"
                               button.isEnabled = false
                           }
                       })
                   }
               }).start()
           }
       }
   }
   ```

   ```kt
   // 코틀린 람다 사용해서 단순화된 형식

   class MainActivity : AppCompatActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)

           val button: Button = findViewById(R.id.button)
           button.setOnClickListener {
               Thread {
                   Log.d("쓰레드", "쓰레드 시작! 1초 뒤 버튼 무효화 시킬 예정")
                   Thread.sleep(1000)
                   runOnUiThread {
                       button.text = "유효하지 않은 버튼"
                       button.isEnabled = false
                   }
               }.start()
           }
       }
   }
   ```

   ## 메시지 큐

   메시지 큐는 쓰레드에서 실행될 작업들(메시지, runnable)의 리스트를 가지고 있는 큐이다. `Looper.myQueue()`를 통해 현재 쓰레드의 메시지 큐를 얻을 수 있다.

   ## 메시지

   메시지란 쓰레드에서 사용될 정보를 담고 있는 객체이다. 핸들러에게 전달될 수 있는 설명 정보와 임의의 데이터를 가지고 있다. 두 개의 int 필드(매개변수)를 가지고 있으며, 하나의 객체 필드를 가지고있다. 쉽게 생각하면 메시지는 데이터 전달에 사용되는 번들과 같은 것이다.

   다음은 메시지에서 유용하게 쓰이는 매개변수들이다

   ```
   public int what - 이 메시지가 무엇에 대한 것인지 수신자에게 알려주는 역할을 한다.
   ```

   ```
   public int arg1

   public int arg2

   setData() 대신 사용할 수 있는 저비용 매개변수들이다. 만약 몇개의 정수 변수만 필요하다면 번들 형식인 data 대신에 사용할 수 있다.
   ```

   ```
   public Object obj -  수신자에게 전달하는 임의의 Object이다. 만약 메시지가 프로세스 사이를 오가는 상황이고, obj가 Parcelable 클래스를 포함한다면(어플리케이션 자체에서 구현된 것 예외) obj는 반드시 non-null 이어야 한다. 그 이외의 데이터 전달은 setData를 사용한다.
   ```

   ## 핸들러

   핸들러는 메시지와 Runnable을 전달하거나 처리하는 역할을 한다. 지정된 메시지나 Runnable을 지닌 채 메시지 큐 안으로 들어가며, 나올 차례가 되었을때 메시지 큐 밖으로 나와서 지정된 작업을 수행한다. 메시지 큐와 붙어서 동작하는 루퍼와 함께 동작하기 때문에 생성될때 루퍼와 함께 생성된다.

   ```
   public Handler(android.os.Looper looper)
   ```

   메인 쓰레드의 루퍼와 연동시키고 싶다면 `Looper.getMainLooper()`을 호출하여 사용하면 된다.

   핸들러의 주요 사용은 다음과 같이 두가지이다

   - 특정 시점에 메시지나 Runnable이 실행하도록 스케쥴하는 것
   - 다른 쓰레드로부터 온 메시지나 Runnable을 현재 속해있는 쓰레드의 메시지 큐로 넣는 것
     // 추가적으로 더 적을 것

   ## 결론

   - 다른 쓰레드에서 처리된 수행 내용을 UI에 표시하기 위해 UI 담당인 메인 쓰레드로 전달한다.
   - 메시지 큐는 처리할 작업 내용들을 담고있다.
   - 메시지와 Runnable은 수행될 작업 내용들을 품은 채 메시지 큐 안에 들어간다
   - 루퍼는 메시지 큐 주위를 반복해서 돌면서 핸들러가 메시지나 Runnable을 품은 채 다가오면 핸들러를 메시지 큐에 넣는다.
   - 루퍼는 메시지 큐 주위를 계속 돌며 차례대로 핸들러를 하나씩 꺼낸다.
   - 핸들러는 지니고 있는 메시지나 Runnable을 가지고 작업을 수행한다.

   ```
   💡
   메시지 큐 안에 있는 핸들러가 꺼내진 후 작업 내용이 처리될 때 사실 콜백 스택에 들어간 뒤 처리된다.
   함수를 실행하면 해당 함수는 콜백 스택에 들어가게 된다. 따라서 핸들러 안의 작업 내용이 함수로 여겨져서 콜백 스택에 들어가는 것이다.

   만약 메시지 큐 안의 작업 내용과 콜백 스택 안의 내용이 동시에 존재한다면 콜백 스택 안의 내용부터 먼저 전부다 처리된 뒤 메시지 큐 안의 내용들이 차례대로 콜백 스택으로 들어가게 된다.
   ```

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
