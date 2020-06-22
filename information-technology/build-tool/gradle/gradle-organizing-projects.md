# Organizing Gradle Projects

모든 소프트웨어 프로젝트의 소스 코드와 빌드 로직은 의미있게 구성되어야 합니다. 아래 설명할 예제들은 어떻게 하면 읽기 쉽고, 유지보수하기 쉬운 프로젝트를 만드는지 실용적인 부분을 설명합니다. 또한, 일반적인 문제들도 다루면서 어떻게 그것들을 피하는지도 이야기 해보겠습니다.

Gradle을 설치한후, 프로젝트 폴더를 하나만들어 아래 명령을 실행해봅시다.

> gradle init

그러면 아래 폴더 및 파일들이 생성될 것이다.

```
project/  
    gradlew  
    gradlew.bat  
    gradle/wrapper/  
        gradle-wrapper.jar  
        gradle-wrapper.properties  
    build.gradle  
    settings.gradle
```

**gradlew, gradlew.bat**은 모두 실행 스크립트입니다. gradlew은 유닉스용 실행 스크립트이고 gradlew.bat은 윈도우 용입니다. 둘의 내용은 동일합니다. 

**settings.gradle 파일**은 프로젝트의 구성 정보를 기록하는 파일입니다. 어떤 하위프로젝트들이 어떤 관계로 구성되어 있는지를 기술합니다. Gradle은 이 파일에 기술된대로 프로젝트를 구성합니다.

**build.gradle 파일**은 의존성이나 플러그인 설정 등을 위한 스크립트 파일입니다.

## Gradle Wrapper

Gradle Wrapper를 사용하는 목적은 이미 존재하는 프로젝트를 새로운 환경에 설치할때 별도의 설치나 설정과정없이 곧 바로 빌드할 수 있게 하기 위함입니다. 심지어 Java나 Gradle도 설치할 필요도 없고, 로컬에 설치된 Gradle 또는 Java의 버전도 신경쓸 필요가 없다. 따라서 항상 Gradle Wrapper를 사용할 것을 권장합니다. 
 
**gradle/wrapper/gradle-wrapper.jar 파일**은 Wrapper 파일입니다. Gradle Wrapper를 이용할때 이 파일들을 기본적으로 사용하기 때문에 로컬 환경의 영향을 받지 않습니다. 필요시에는 Wrapper 버전에 맞는 구성들을 로컬 캐시에 다운로드 받습니다. 

**gradle/wrapper/gradle-wrapper.properties 파일**은 Wrapper 설정 파일입니다. 이 파일에 사용할  Wrapper 버전 등을 저장하는데, 버전을 변경하면 task 실행시, 자동으로 새로운 Wrapper 파일을 로컬 캐시에 다운로드 받습니다.

Gradle로 컴파일이나 빌드 등을 할때, 아래와 같이 하면 로컬에 설치된 gradle을 사용합니다.

> gradle build

이 경우 Java나 Gradle이 설치되어 있어야 하고, 새로받은 프로젝트의 Gradle 버전과 로컬에 설치된 Gradle 버전이 호환되지 않으면 문제가 발생할 수 있습니다. 따라서 Wrapper를 사용하면 아래와 같이 실행합니다.

> ./gradlew build

# 멀티 프로젝트 구성

이제 기본적인 설정이 끝났다. 이제 폴리글랏 언어를 지원하는 멀티 프로젝트로 구성해 보자. 먼저 settings.gradle 파일을 열고 아래와 같이 셋팅하자.

rootProject.name = 'algorithm'  
  
include 'java'  
include 'kotlin'

**rootProject.name**은 최상위 프로젝트의 이름이다. 기본적으로는 프로젝트 폴더명으로 만들어진다.

그리고 algorithm 프로젝트의 하위프로젝트로 java, kotlin를 포함시켰다. 여기서 만약 하위 프로젝트의 하위 프로젝트를 만드려면  `include 'java::sub'`  와 같이 할 수 있다.

이제 하위 프로젝트 폴더를 만들자. IDE를 활용하면 간단하게 하위 프로젝트를 생성할 수 있지만, 여기서는 수동으로 만들어 볼 것이다.

> mkdir java  
> mkdir kotlin

그리고 각각의 하위 프로젝트의 기본 폴더 트리를 생성한다.

> mkdir -p java/src/main/java  
> mkdir -p kotlin/src/main/kotlin

여기서는 각 언어의 기본 폴더 구조가 비슷하지만, 언어에 따라서 다를 수 있다. 여기서 각 하위 프로젝트의 폴더명은 java, kotlin으로 생성되었지만, 모듈명은 기본으로 algorithm-java, algorithm-kotlin이 된다. IDE로 프로젝트를 열어보면 이러한 폴더명과 모듈명을 확인할 수 있다. 또한 모듈명을 직접 변경할 수도 있다.

이제 각 하위 폴더를 Gradle 프로젝트로 만들 차례다. 최상위 폴더에 build.gradle 파일이 존재하는 것 처럼, 각 하위 프로젝트의 상위에도 build.gradle 파일을 만들자.

> touch java/build.gradle  
> touch kotlin/build.gradle

마지막으로 최상위 프로젝트의 build.gradle 파일에 모든 하위 프로젝트에 적용되는 공통 설정을 적용하자.

subprojects {  
    group = "funfunstudy"    // 생성될 아티팩트의 그룹명  
  
    repositories {  
        mavenCentral()  
    }  
  
    dependencies {  
    }  
}

**subprojects**내의 설정값들은 모든 하위 프로젝트에 적용될 것이다. 만약 최상위 프로젝트를 포함한 모든 하위 프로젝트에 공통으로 적용하고 싶다면,  **allprojects**를 사용할 수 있다.



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

> Source Set
> Source Set이란 소스 기반 프로젝트를 빌딩하는데 Gradle 자바가 지원하는 새로운 개념입니다. 가장 중요한 아이디어는 소스 파일들과 자원은 종종 타입별로 모인다는 것입니다. 타입 예로는 application 코드, unit tests 그리고 통합 테스트 정도가 있겠습니다. 이렇듯 Source Set의 목적은 소스들를 논리적 그룹으로 묶고 그 목적을 설명하는데 있습니다. 
> Java Source Set 
    -   main : 실제 작동 소스코드. 컴파일해서 JAR 파일로 들어감.
    -   test : 단위 테스트 소스코드. 컴파일해서 JUnit이나 TestNG로 실행.

Example 1. Integration test source set

```groovy
// gradle/integration-test.gradle 
sourceSets {
    integTest { //sourceSets으로 integTest를 정의 
        java.srcDir file('src/integTest/java')
        resources.srcDir file('src/integTest/resources')
        compileClasspath += sourceSets.main.output + configurations.testRuntimeClasspath
        runtimeClasspath += output + compileClasspath
    }
}
```
source sets은 소스 코드를 컴파일 하는데 책임이 있습니다. 그러나 byte code를 실행하는 것을 다루진 않습니다. test 실행을 위해서, Test 타입에 task가 정의 되어야 합니다. 

```groovy
// gradle/integration-test.gradle
task integTest(type: Test) { //Test 타입 task 정의
    description = 'Runs the integration tests.'
    group = 'verification'
    testClassesDirs = sourceSets.integTest.output.classesDirs
    classpath = sourceSets.integTest.runtimeClasspath
    mustRunAfter test
}
check.dependsOn integTest
```

## [Always define a settings file](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#always_define_a_settings_file)

Gradle은 매번 빌드가 발생할때 마다 `settings.gradle`를 찾습니다. 이를 위해서 root 디렉토리부터 하위 디렉토리를 전부 찾기 때문에, 항상`settings.gradle`을 root 디렉토리에 넣도록 합시다. 

전형적인 Gradle 프로젝트는 아래와 같습니다. 

```groovy
.
├── build.gradle
└── settings.gradle
```



# References
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAzOTQwNTQzMSwyMDUxNDk2MjkwLDQ1MT
Q0MDQyNyw0ODUyMTMzMzYsLTE5OTc5NTQ4NTQsMTE5NDEyNzEy
Nyw2MTMyMTQ3MDcsMTc1NzkzNjI5MiwtMTc1Mjk5NTYxNCwtNT
c3MjczMzk0LDIwMjUwNDY4MjYsMTcyMzU2NjMwNV19
-->