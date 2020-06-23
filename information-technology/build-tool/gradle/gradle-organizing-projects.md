# Organizing Gradle Projects

모든 소프트웨어 프로젝트의 소스 코드와 빌드 로직은 의미있게 구성되어야 합니다. 아래 설명할 예제들은 어떻게 하면 읽기 쉽고, 유지보수하기 쉬운 프로젝트를 만드는지 실용적인 부분을 설명합니다. 또한, 일반적인 문제들도 다루면서 어떻게 그것들을 피하는지도 이야기 해보겠습니다.

Gradle을 설치한후, 프로젝트 폴더를 하나만들어 아래 명령을 실행해봅시다.

> gradle init

그러면 아래 폴더 및 파일들이 생성됩니다.

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

## settings.gradle

```
rootProject.name = 'tropicana'  
  
include 'core'  
include 'gateway'  
include 'internal'  
include 'batch'  
include 'client'  
  
if( file('../soda').exists() ) {  
    includeBuild '../soda'  
}
```
>

rootProject.name은 최상위 프로젝트의 이름을 말합니다. 기본적으로는 프로젝트 폴더명으로 만들어집니다. 
그리고 tropicana 프로젝트의 하위프로젝트로 core, gateway, internal ... 등이 있습니다. 루트 프로젝트 하위에 프로젝트 폴더가 있어야 합니다.

Gradle은 매번 빌드가 발생할때 마다 `settings.gradle`를 찾습니다. 이를 위해서 root 디렉토리부터 하위 디렉토리를 전부 찾습니다. 빌드 성능을 위해서라도 반드시 `settings.gradle`을 root 디렉토리에 넣도록 합시다. 

## build.gradle

최상위 프로젝트의 build.gradle 파일에 설정된 내용은 모든 하위 프로젝트에 공통적으로 적용할 수 있습니다. 

buildscript 블럭은 기본적으로, Gradle 스스로를 위한 변수, repositories, dependencies등을 설정하는데 사용합니다. 여기서 Gradle 자신이 아닌 다른 모듈에 대한 의존성은 buildscript 블럭에 적으면 안됩니다.

```
// build.gradle
buildscript {
	//ext 전역변수; 프로젝트 전체 또는 서브 프로젝트에서도 접근 가능한 변수
	ext {
	_versions = [  springBoot : '2.2.4.RELEASE',  
	                slf4j : '1.7.25',  
	                lombok : '1.16.20',  
	                ... 
                ]
    }
	// Gradle이 의존성 dependencies을 검색 또는 다운로드할 때, 사용할 repositories를 설정.
	// JCenter,Maven Central 그리고 Ivy 같은 외부 저장소가 사용가능.
	 repositories {  
		  mavenCentral()  
		  maven { url "https://plugins.gradle.org/m2/" }  
		  maven { url "http://repo.linecorp.com/content/repositories/releases/" }  
		  maven { url "http://repo.linecorp.com/content/repositories/snapshots/" }  
	 }  
	 
	 // Gradle이 프로젝트를 빌드하기 위해 필요한 의존성을 설정.
	 // 멀티 프로젝트를 구성할때는 root buildscript에 설정된 의존성을 모든 하위 프로젝트 buildscript에서 사용가능.
	 dependencies {  
		 classpath("org.springframework.boot:spring-boot-gradle-plugin:${_versions.springBoot}")  
		 classpath("io.spring.gradle:dependency-management-plugin:1.0.2.RELEASE")  
    }  
}
```

subprojects 블럭에 설정값들은 모든 하위 프로젝트에 적용됩니다. 최상위 프로젝트를 포함한 모든 하위 프로젝트에 공통으로 적용하고 싶다면, subprojects 블럭이 아닌 allprojects 블럭을 사용하면 됩니다.

```
// build.gradle
subprojects {
	apply plugin: 'java'        // 'java'라는 Gradle 플러그인 적용
	sourceCompatibility = 1.8   // Java 호환 버전을 1.8로 설정
	group = 'ai.clova.tropicana' // 생성될 아티팩트 그룹의 이름

	repositories {  //하위 프로젝트가 사용할 의존성을 가져올 주소 설정
	  mavenCentral()  
	  ...
	}  
	dependencies { 
		// 컴파일 타임에 의존성을 받아옴
		compile("org.slf4j:slf4j-api:${_versions.slf4j}")  
		// 컴파일할때는 사용하고, 아티팩트를 만들때는 포함하지 않음
		compileOnly("org.projectlombok:lombok:${_versions.lombok}")  
		// 테스트시만 의존성을 받아옴, 아티팩트를 만들때는 포함하지 않음
		testCompileOnly("org.projectlombok:lombok:${_versions.lombok}")  
		// 실행할때 의존성을 받아옴(기본적으로 컴파일을 모두 포함)    
		runtime('org.hibernate:hibernate:3.0.5')
	}
```

최상위 폴더에 build.gradle 파일이 존재하는 것 처럼, 각 하위 프로젝트의 상위에도 build.gradle 파일을 만들어 유지보수 할 수 있습니다.

## src/main/... 

Gradle의 language 플러그인은 소스 코드를 발견하고 컴파일 하는데 기본 컨벤션으로 판별합니다.  예를 들어, [Java plugin](https://docs.gradle.org/current/userguide/java_plugin.html#java_plugin)을 사용하는 프로젝트는 자동적으로 `src/main/java`의 디렉토리의 코드를 컴파일합니다. 다른 플러그인도 같은 패턴을 따릅니다. 

어떤 컴파일러는 같은 소스 디렉토리에서 다수의 언어를 크로스 컴파일링이 가능합니다. Groovy 컴파일러의 경우, `src/main/groovy`에 있는 자바와 Groovy 코드가 혼합된 소스도 다룰 수 있습니다. 

Gradle에선 language에 따라 디렉토리를 나누고 대응하는 소스를 해당 디렉토리에 저장하는 것을 추천합니다. 왜냐하면 빌드가 더 잘 동작할 뿐만 아니라 기본 설정이기 때문에 사용자와 빌드 모두가 편하기 때문입니다.

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

## [Compiling](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:compile)

실제 생성 코드와 테스트 코드를 컴파일하는건 아래 컨벤션을 따르면 비교적 

Compiling both your production and test code can be trivially easy if you follow the conventions:

1.  Put your production source code under the  _src/main/java_  directory
    
2.  Put your test source code under  _src/test/java_
    
3.  Declare your production compile dependencies in the  `compileOnly`  or  `implementation`  configurations (see previous section)
    
4.  Declare your test compile dependencies in the  `testCompileOnly`  or  `testImplementation`  configurations
    
5.  Run the  `compileJava`  task for the production code and  `compileTestJava`  for the tests
    
### [파일과 디렉토리 위치 바꾸기](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:custom_java_source_set_paths)

Each source set defines where its source code resides, along with the resources and the output directory for the class files. You can override the convention values by using the following syntax:

```groovy
sourceSets {
    main {
         java {
            srcDirs = ['src']
         }
    }

    test {
        java {
            srcDirs = ['test']
        }
    }
}
```

sourceSets {  
  main {  
  resources {  
  srcDirs "src/main/resources", "src/main/resources-${profile}"  
  }  
 }}

Now Gradle will only search directly in  _src_  and  _test_  for the respective source code. What if you don’t want to override the convention, but simply want to  _add_  an extra source directory, perhaps one that contains some third-party source code you want to keep separate? The syntax is similar:

Example 4. Declaring custom source directories additively

`Groovy``Kotlin`

build.gradle

```groovy
sourceSets {
    main {
        java {
            srcDir 'thirdParty/src/main/java'
        }
    }
}
```

Crucially, we’re using the  _method_  `srcDir()`  here to append a directory path, whereas setting the  `srcDirs`  property replaces any existing values. This is a common convention in Gradle: setting a property replaces values, while the corresponding method appends values.

You can see all the properties and methods available on source sets in the DSL reference for  [SourceSet](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.SourceSet.html)  and  [SourceDirectorySet](https://docs.gradle.org/current/dsl/org.gradle.api.file.SourceDirectorySet.html). Note that  `srcDirs`  and  `srcDir()`  are both on  `SourceDirectorySet`.



## [Separate source files per test type](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:separate_test_type_source_files)

한 프로젝트에서 여러 종류의 테스트를 정의하고 실행하는 것은 자주 있는 일입니다. (unit tests, integration tests, functional test or smoke tests와 같은)

최적으로, 각 타입의 테스트 소스 코드는 소스 디렉토리에 국한된 곳에 저장되어 있어야 합니다. 이렇게 나눠진 테스트 코드는 각 테스트 타입을 독립적으로 수행할 수 있어 유지보수와 관심사의 분리에 좋습니다. 

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

# References

[https://medium.com/@goinhacker/%EC%9A%B4%EC%98%81-%EC%9E%90%EB%8F%99%ED%99%94-1-%EB%B9%8C%EB%93%9C-%EC%9E%90%EB%8F%99%ED%99%94-by-gradle-7630c0993d09](https://medium.com/@goinhacker/%EC%9A%B4%EC%98%81-%EC%9E%90%EB%8F%99%ED%99%94-1-%EB%B9%8C%EB%93%9C-%EC%9E%90%EB%8F%99%ED%99%94-by-gradle-7630c0993d09)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI4OTcyODI1MiwtMjEwNzg5NTQ4NywyOD
I4MzkyMCwxNzk2NDc5MzYwLDE2NDY2MjE4MzEsNDcyMzA3NjYs
MjEyODY3MjkzNiwtMTgyNDUxMjEyNiw2MTU0NzAxNzEsLTU3Nz
Q5MDM0MSwtOTMzNjIxMTIwLDkyMjYxOTY0NCwtOTA0MzI4NjY4
LC0xODc0OTA2NDY4LC0xNDg2MjgxOTkxLDk5ODY5NTUxLDUxOD
A5NzM0NiwyMDUxNDk2MjkwLDQ1MTQ0MDQyNyw0ODUyMTMzMzZd
fQ==
-->