# Organizing Gradle Projects

모든 소프트웨어 프로젝트의 소스 코드와 빌드 로직은 의미있게 구성되어야 합니다. 아래 설명할 예제들은 어떻게 하면 읽기 쉽고, 유지보수하기 쉬운 프로젝트를 만드는지 실용적인 부분을 설명합니다. 또한, 일반적인 문제들도 다루면서 어떻게 그것들을 피하는지도 이야기 해보겠습니다.

## [Separate language-specific source files](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_language_source_files)

Gradle의 language 플러그인은 소스 코드를 발견하고 컴파일 하는데 컨벤션을 판별합니다. 예를 들어, [Java plugin](https://docs.gradle.org/current/userguide/java_plugin.html#java_plugin)을 사용하는 프로젝트는 자동적으로 `src/main/java`의 디렉토리의 코드를 컴파일합니다. 다른 플러그인도 같은 패턴을 따릅니다. 

디렉토리 경로의 마지막 일부는 기대되는 언어의 소스 파일들 나타냅니다.

어떤 컴파일러는 같은 소스 디렉토리에서 다수의 언어를 크로스 컴파일링이 가능합니다. Groovy 컴파일러의 경우, `src/main/groovy`에 있는 자바와 Groovy 코드가 혼합된 소스도 다룰 수 있습니다. 

언어에 따라 디렉토리를 나누고 언어에 맞는 소스를 할당하는것을 Gradle에선 추천하고 있습니다. 왜냐하면 빌드가 더 잘 동작할 뿐만 아니라 사용자와 빌드 모두가 기본 설정을 따라갈 수 있어 편하기 때문입니다.

아래 소스 트리는 자바와 코틀린 소스 파일들을 가지고 있습니다. 자바 파일은 `src/main/java`에 있고 Kotlin 파일은 `src/main/kotlin`에 있습니다.

```groovy.
├── build.gradle
├── settings.gradle
└── src
    └── main
        ├── java
        │   └── HelloWorld.java
        └── kotlin
            └── Utils.kt
```

## [](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_test_type_source_files)[Se](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_test_type_source_files)


[https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#organizing_gradle_projects](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#organizing_gradle_projects)


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTQwNjA1MzkwLDE3NTc5MzYyOTIsLTE3NT
I5OTU2MTQsLTU3NzI3MzM5NCwyMDI1MDQ2ODI2LDE3MjM1NjYz
MDVdfQ==
-->