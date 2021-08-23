# Why Kotlin?
안드로이드는 Java 또는 Kotlin으로 개발한다. 두 언어 모두 알면 좋겠지만 지금은
Kotlin을 공부하기로 선택했다. 그 이유는 Java 보다 최근에 나온 언어로 비교적 쉽고,
Java와도 호환이이 가능하다. 그리고 Google에서 안드로이드 앱을 만들기 좋은 언어로
공식적으로 선언했기 때문이다.
+ __JDK > JRM > JVM__
Java 와 Kotlin으로 Application을 개발하고 실행하기 위해서는 JDK를 설치해야 한다.
JDK(Java Development Kit)에는 컴파일러와 디버깅도구와 같은 개발도구와 함께 JRM이
포함되어 있고, JRM(Java Runtime Environment)에는 프로그램 실행에 필요한 라이브러리,
JVM을 포함한다.

JVM(Java Virtual Machine)은 Java 프로그램이 어느 기기 또는 어떠한 운영체제 상에서도
실행될 수 있게하는 기능과 프로그램 메모리를 관리하고 최적화 하는 2가지 기능이 있다.
C언어는 개발자가 malloc, free를 통해 메모리를 코드로 작성해 직접 관리했으나 Java는
이러한 기능을 JVM에서 처리한다. 또한 C언어로 작성된 소스코드는 컴파일러를 통해서
컴파일 되어 운영체제로 전달되지만 Java는 소스코드가 컴파일러를 통해 바이트코드로 전환
되고, 이 바이트코드가 JVM를 거쳐 각 운영체제에 특화된 파일로 전환되어 실행된다.
따라서 Java와 Kotlin은 같은 컴파일러를 통해 바이트코드로 전환되기 때문에 호환이 가능하다.