# Organizing Gradle Projects

모든 소프트웨어 프로젝트의 소스 코드와 빌드 로직은 의미있게 구성되어야 합니다. 아래 설명할 예제들은 어떻게 하면 읽기 쉽고, 유지보수하기 쉬운 프로젝트를 만드는지 실용적인 부분을 설명합니다. 또한, 일반적인 문제들도 다루면서 어떻게 그것들을 피하는지도 이야기 해보겠습니다.

# [Separate language-specific source files](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_language_source_files)

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

# [Separate source files per test type](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_test_type_source_files)

한 프로젝트에서 여러 종류의 테스트를 정의하고 실행하는 것은 자주 있는 일입니다. (unit tests, integration tests, functional test or smoke tests와 같은)

최적으로, 각 타입의 테스트 소스 코드 소스 디렉토리에 국한된 곳에 저장되어 있어야 합니다. 이렇게 나눠진 테스트 코드는 각 테스트 타입을 독립적으로 수행할 수 있어 유지보수와 관심사의 분리에 좋습니다. 

아래 소스 트리는 자바 기반 프로젝트에서 integration test를 어떻게 나누는지 보여줍니다.

```groovy.
├── build.gradle
├── gradle
│   └── integration-test.gradle
├── settings.gradle
└── src
    ├── integTest
    │   └── java
    │       └── DefaultFileReaderIntegrationTest.java
    ├── main
    │   └── java
    │       ├── DefaultFileReader.java
    │       ├── FileReader.java
    │       └── StringUtils.java
    └── test
        └── java
            └── StringUtilsTest.java
```

Gradle은 [source set concept](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:java_source_sets)의 도움을 받아 소스 코드 디렉토리를 모델링 합니다. 
소스 집단 객체 하나를 다수의 소스 코드 디렉토리를 가리키도록 하여, Gradle은 자동적으로 대응하는 컴파일 task를 만들어냅니다.

## Source Set

Source Set이란 소스 기반 프로젝트를 빌딩하는데 Gradle 자바가 지원하는 새로운 개념입니다. 가장 중요한 아이디어는 소스 파일들과 자원은 종종 타입별로 모인다는 것입니다. 여기서 타입의 예로는 application 코드, unit tests 그리고 통합 테스트 정도가 있겠습니다. 이런 로직 그룹은 전형적으로 고유한 파일 의존성과 classpath 등을 가집니다. 확실히, 한 source set을 형성하는 파일들은 같은 디렉토리에 위치할 필요가 없습니다. 



Source sets are a powerful concept that tie together several aspects of compilation:

-   the source files and where they’re located
    
-   the compilation classpath, including any required dependencies (via Gradle  [configurations](https://docs.gradle.org/current/userguide/dependency_management_terminology.html#sub:terminology_configuration))
    
-   where the compiled class files are placed
    

You can see how these relate to one another in this diagram:

![java sourcesets compilation](https://docs.gradle.org/current/userguide/img/java-sourcesets-compilation.png)

Figure 1. Source sets and Java compilation

The shaded boxes represent properties of the source set itself. On top of that, the Java Library Plugin automatically creates a compilation task for every source set you or a plugin defines — named  `compile_SourceSet_Java`  — and several  [dependency configurations](https://docs.gradle.org/current/userguide/java_plugin.html#java_source_set_configurations).

The  `main`  source set

Most language plugins, Java included, automatically create a source set called  `main`, which is used for the project’s production code. This source set is special in that its name is not included in the names of the configurations and tasks, hence why you have just a  `compileJava`  task and  `compileOnly`  and  `implementation`  configurations rather than  `compileMainJava`,  `mainCompileOnly`  and  `mainImplementation`  respectively.

Java projects typically include resources other than source files, such as properties files, that may need processing — for example by replacing tokens within the files — and packaging within the final JAR. The Java Library Plugin handles this by automatically creating a dedicated task for each defined source set called  `process_SourceSet_Resources`  (or  `processResources`  for the  `main`  source set). The following diagram shows how the source set fits in with this task:

![java sourcesets process resources](https://docs.gradle.org/current/userguide/img/java-sourcesets-process-resources.png)

Figure 2. Processing non-source files for a source set

As before, the shaded boxes represent properties of the source set, which in this case comprises the locations of the resource files and where they are copied to.

In addition to the  `main`  source set, the Java Library Plugin defines a  `test`  source set that represents the project’s tests. This source set is used by the  `test`  task, which runs the tests. You can learn more about this task and related topics in the  [Java testing](https://docs.gradle.org/current/userguide/java_testing.html#java_testing)  chapter.

Projects typically use this source set for unit tests, but you can also use it for integration, acceptance and other types of test if you wish. The alternative approach is to  [define a new source set](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:custom_java_source_sets)  for each of your other test types, which is typically done for one or both of the following reasons:

-   You want to keep the tests separate from one another for aesthetics and manageability
    
-   The different test types require different compilation or runtime classpaths or some other difference in setup
        




Example 1. Integration test source set

```groovy
// gradle/integration-test.gradle 
sourceSets {
    integTest {
        java.srcDir file('src/integTest/java')
        resources.srcDir file('src/integTest/resources')
        compileClasspath += sourceSets.main.output + configurations.testRuntimeClasspath
        runtimeClasspath += output + compileClasspath
    }
}
```

Source sets are only responsible for compiling source code, but do not deal with executing the byte code. For the purpose of test execution, a corresponding task of type  [Test](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.testing.Test.html)  needs to be established.

Example 2. Integration test task

`Groovy``Kotlin`

gradle/integration-test.gradle

```groovy
task integTest(type: Test) {
    description = 'Runs the integration tests.'
    group = 'verification'
    testClassesDirs = sourceSets.integTest.output.classesDirs
    classpath = sourceSets.integTest.runtimeClasspath
    mustRunAfter test
}

check.dependsOn integTest
```

## [](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:use_standard_conventions)[Us](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:use_standard_conventions)



## [](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_test_type_source_files)[Se](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_test_type_source_files)


[https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#organizing_gradle_projects](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#organizing_gradle_projects)


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE5NDEyNzEyNyw2MTMyMTQ3MDcsMTc1Nz
kzNjI5MiwtMTc1Mjk5NTYxNCwtNTc3MjczMzk0LDIwMjUwNDY4
MjYsMTcyMzU2NjMwNV19
-->