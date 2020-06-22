# Organizing Gradle Projects

모든 소프트웨어 프로젝트의 소스 코드와 빌드 로직은 의미있게 구성되어야 합니다. 아래 설명할 예제들은 어떻게 하면 읽기 쉽고, 유지보수하기 쉬운 프로젝트를 만드는지 실용적인 부분을 설명합니다. 또한, 일반적인 문제들도 다루면서 어떻게 그것들을 피하는지도 이야기 해보겠습니다.

## [Separate language-specific source files](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_language_source_files)

Gradle의 language 플러그인은 소스 코드를 발견하고 컴파일 하는데 컨벤션을 판별합니다. 예를 들어, [Java plugin](https://docs.gradle.org/current/userguide/java_plugin.html#java_plugin)을 사용하는 프로젝트는 자동적으로 `src/main/java`의 디렉토리의 코드를 컴파일합니다. 다른 플러그인도 같은 패턴을 따릅니다. 

디렉토리 경로의 마지막 일부는 기대되는 언어의 소스 파일들 나타냅니다.

어떤 컴파일러는 같은 소스 디렉토리에서 다수의 언어를 크로스 컴파일링이 가능합니다. 

Other language plugins follow the same pattern. The last portion of the directory path usually indicates the expected language of the source files.

Some compilers are capable of cross-compiling multiple languages in the same source directory. The Groovy compiler can handle the scenario of mixing Java and Groovy source files located in  `src/main/groovy`. Gradle recommends that you place sources in directories according to their language, because builds are more performant and both the user and build can make stronger assumptions.

The following source tree contains Java and Kotlin source files. Java source files live in  `src/main/java`, whereas Kotlin source files live in  `src/main/kotlin`.

`Groovy``Kotlin`

```groovy
.
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
eyJoaXN0b3J5IjpbLTE2MTA1Njk4NDcsLTU3NzI3MzM5NCwyMD
I1MDQ2ODI2LDE3MjM1NjYzMDVdfQ==
-->