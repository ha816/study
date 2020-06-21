# Overview

Gradle은 오픈 소스 빌드 자동화 도구입니다. 

>Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that is designed to be flexible enough to build almost any type of software. 

Gradle은 빌드 성능을 높이기 위한 많은 최적화 기법을 사용하고 있습니다. 대표적으로  불필요한 빌드 작업을 줄이기 위해 build cache를 적극 활용합니다. 

Gradle은 [Maven Conventions](https://maven.apache.org/maven-conventions.html)을 따릅니다. 

Gradle은 Java 기반입니다. 따라서 사용하기 위해 JDK(Java Development Kit)가 반드시 필요합니다. 그러나 Gradle은 JVM 프로젝트 빌드에만 국한되지 않고 다양한 소프트웨어를 빌드할 수 있습니다.  당연히 플러그인도 빌드에 활용할 수 있습니다.

# Concepts

## [1. Gradle is a general-purpose build tool](https://docs.gradle.org/current/userguide/what_is_gradle.html#1_gradle_is_a_general_purpose_build_tool)

Gradle을 활용하면 어떤 소프트웨어도 빌드가 가능합니다. 왜냐하면 어떤 프로젝트를 어떻게 빌드 하든 Gradle의 제약이 거의 없기 때문입니다. 

단 한 가지 주의할 것은 현재 Gradle 의존성 관리는 [Maven Dependency Management](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)와 Ivy-compatible repositories만 지원하고 있습니다.

## [2. The core model is based on tasks](https://docs.gradle.org/current/userguide/what_is_gradle.html#the_core_model_is_based_on_tasks)

Gradle은 tasks로 구성된 Directed Acyclic Graphs(DAGs)로 전체 빌드 과정을 모델링합니다. 즉 하나의 빌드는 반드시 tasks와 tasks간의 의존성을 나타내는 DAG를 만든다는 의미입니다. 그래프가 생성되면, Gradle은 어떤 task가 어떤 순서로 동작해야 하는지 판단하고 작업을 수행합니다.

![Example task graphs](https://docs.gradle.org/current/userguide/img/task-dag-examples.png)

위 두 그림은 task 그래프의 예제로 화살은 task간의 의존성을 나타냅니다. 
왼쪽 그림은 추상화된 일반적 task 그래프를 나타냅니다. 오른쪽은 실제 표준 자바를 빌드하는 과정을 표현한 task 그래프입니다.

대부분의 어떤 빌드 과정도 task 그래프로 모델링될 수 있습니다. 그리고 이것이 Gradle이 유연해질 수 있는 이유입니다. 

task 그래프는 외부 플러그인 또는 Gradle의 [build script](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)으로도 모델링 가능합니다.

### Task

하나의 Task는 Actions, Inputs, Outputs로 구성됩니다.
* Actions
	* 복사 또는 소스 컴파일과 같은 작은 작업들
* Inputs
	* action을 해야할 파일 또는 디렉토리와 같은 값들
* Outputs
	* action을 통해 수정하거나 생성한 파일 또는 디렉토리들

각 구성요소는 task가 해야할것에 따라 선택적입니다. 예를 들어, [standard lifecycle tasks](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks) 같은 task는 어떤 actions도 가지지 않는 경우도 있습니다. 

빌드에 필요한 특정 task만 골라 돌리면, task를 새로 만들기 위한 자원을 아낄 수 있다. 만약 unit test를 돌려보고 싶다면, `test` task를 찾으면 됩니다. 만약 애플리케이션을 package하고 싶다면, 일반적으로 `assemble` task를 수행하면 됩니다.

## [3. Gradle has several fixed build phases](https://docs.gradle.org/current/userguide/what_is_gradle.html#3_gradle_has_several_fixed_build_phases)

Gradle은 Initialization, Configuration, Execution의 세 단계로 build scripts를 평가하고 실행합니다. 그리고 이 세 가지 단계는 Gradle의 [Build Lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html#build_lifecycle)의 핵심입니다.

* Initialization
	* Sets up the environment for the build and determine which projects will take part in it.
	* 빌드를 위한 환경을 구축하고 어떤 프로젝트들이 참가할지 판별하는 단계.
* Configuration
	* 빌드를 위한 task 그래프를 만들고 조정한 뒤, 사용자가 원하는 task에 따라 어떤 순서로 어떤 tasks가 필요한지 판단하는 단계.
* Execution
	* Configuration 단계가 끝난 후, 선택된 tasks를 수행한다.
   
## [4. Gradle is extensible in more ways than one](https://docs.gradle.org/current/userguide/what_is_gradle.html#4_gradle_is_extensible_in_more_ways_than_one)

Gradle의 기본 빌드 로직만을 따라서 프로젝트를 만들 수 있으면 좋겠지만, 그런 경우는 거의 없습니다. 대부분의 빌드에선 커스텀 빌드 로직을 추가해야할 경우가 많습니다. Gradle은 커스텀 빌드 로직을 위한 몇 가지 메커니즘을 제공합니다. 

* [Custom task types](https://docs.gradle.org/current/userguide/custom_tasks.html)    
* Custom task actions    
* [Extra properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties)  on projects and tasks
* Custom conventions.
* [A custom model](https://guides.gradle.org/implementing-gradle-plugins/#modeling_dsl_like_apis)

## [5. Build scripts operate against an API](https://docs.gradle.org/current/userguide/what_is_gradle.html#5_build_scripts_operate_against_an_api)

Gradle의 빌드 스크립트는 실제 실행 코드입니다. 빌드 스크립트가 코드인 사실 덕분에 Gradle의 유연함이 온다고 생각할 수 있는데, 이는 사실이 아닙니다.

잘 설계된 빌드 스크립트는 소프트웨어 필드를 위해 어떤 스텝들이 필요한지 나타냅니다, 어떻게 이런 스텝들이 동작해야하는 지가 아니라. 이것이 커스텀 task 타입과 플러그인이 해야할 일 입니다.

아직 빌드 스크립트를 실행 가능한 코드로 보는 것이 유용한 영역이 있습니다. 바로 어떻게 빌드 스크립트가 Gradle's API에 대응하는지 문맥을 이해하는 것에는 유용합니다.

Gradle's API는 [Groovy DSL Reference](https://docs.gradle.org/current/dsl/)과 [Javadocs](https://docs.gradle.org/current/javadoc/)로 작성 되어있습니다.

한 빌드 스크립트의 

What do these mean within the context of a build script? Check out the  [Groovy Build Script Primer](https://docs.gradle.org/current/userguide/groovy_build_script_primer.html#groovy_build_script_primer)  to learn the answer to that question so that you can make effective use of the API documentation.

As Gradle runs on the JVM, build scripts can also use the standard  [Java API](https://docs.oracle.com/javase/8/docs/api). Groovy build scripts can additionally use the Groovy APIs, while Kotlin build scripts can use the Kotlin ones.

As we recommend in our best practices, you should  [avoid putting much, if any, imperative logic in your build scripts](https://docs.gradle.org/current/userguide/authoring_maintainable_build_scripts.html#sec:avoid_imperative_logic_in_scripts).	

# Configuration

#### Gradle Wrapper를 사용하는 목적

-   이미 존재하는 프로젝트를 새로운 환경에 설치할때 별도의 설치나 설정과정없이 곧 바로 빌드할 수 있게 하기 위함(Java나 Gradle도 설치할 필요가 없음. 또한 로컬에 설치된 Gradle 또는 Java의 버전도 신경쓸 필요가 없음. 따라서 항상 Wrapper를 사용할 것을 권장.)

#### gradlew 파일

-   유닉스용 실행 스크립트.
-   Gradle로 컴파일이나 빌드 등을 할때, 아래와 같이 하면 로컬에 설치된 gradle을 사용.

```
> gradle build

```

-   이 경우 Java나 Gradle이 설치되어 있어야 하고, 새로받은 프로젝트의 Gradle 버전과 로컬에 설치된 Gradle 버전이 호환되지 않으면 문제가 발생할 수 있음. 따라서 Wrapper를 사용하면 아래와 같이 실행.

```
> ./gradlew build

```

#### gradle.bat 파일

-   원도우용 실행 배치 스크립트.
-   원도우에서 실행 가능하다는 점만 제외하면 gradlew와 동일.

#### gradle/wrapper/gradle-wrapper.jar 파일

-   Wrapper 파일.
-   gradlew나 gradlew.bat 파일이 프로젝트 내에 설치하는 이 파일을 사용하여 gradle task를 실행하기 때문에 로컬 환경의 영향을 받지 않음.**(실제로는 Wrapper 버전에 맞는 구성들을 로컬 캐시에 다운로드 받음)**

#### gradle/wrapper/gradle-wrapper.properties 파일

-   Gradle Wrapper 설정 파일.
-   이 파일의 wrapper 버전 등을 변경하면 task 실행시, 자동으로 새로운 Wrapper 파일을 로컬 캐시에 다운로드 받음.

#### build.gradle 파일

-   의존성이나 플러그인 설정 등을 위한 스크립트 파일.

#### settings.gradle 파일

-   프로젝트의 구성 정보를 기록하는 파일.
-   어떤 하위프로젝트들이 어떤 관계로 구성되어 있는지를 기술.
-   Gradle은 이 파일에 기술된대로 프로젝트를 구성함.

#### 의존성 관리

-   repositories를 사용해서 의존성을 가져올 주소를 설정.
-   dependencies를 사용해서 설정된 Repository에서 가져올 아티팩트를 설정.

```
allprojects {
    repositories {
        mavenCentral()
        jcenter()
        maven {
            url "http://repo.mycompany.com/maven2"
        }
        ivy {
            url "../local-repo"
        }
    }

    dependencies {
        // 로컬 jar 파일의 의존성 설정
        compile fileTree(dir: 'libs', include: '*.jar')
        // 로컬 프로젝트간 의존성 설정
        compile project(':shared')
        // 컴파일 타임에 의존성을 받아옴
        compile 'com.google.guava', name: 'guava:23.0'
        // 테스트시만 의존성을 받아옴
        // 마이너 버전을 '+'로 설정해서 항상 4점대 최신 버전을 사용
        testCompile group: 'junit', name: 'junit', version: '4.+'
        // 컴파일할때는 사용하고, 아티팩트를 만들때는 포함하지 않음
        compileOnly 'org.projectlombok:lombok:1.16.18'
        // 실행할때 의존성을 받아옴(기본적으로 컴파일을 모두 포함)
        runtime('org.hibernate:hibernate:3.0.5')
    }
}

```

-   uploadArchives를 사용해서 생성된 아티팩트를 배포하기 위한 주소를 설정.

```
uploadArchives {
    repositories {
        ivy {
            credentials {
                username "username"
                password "pw"
            }
            url "http://repo.mycompany.com"
        }
    }
}

```

#### Gradle 스크립트의 이해

-   Gradle 스크립트는 groovy를 사용해서 만든 DSL. (DSL이란 특정 도메인에 특화된 언어를 말함)
-   모든 Gradle 스크립트는 두 가지 개념(projects와 tasks)으로 구성.
    -   모든 Gradle 빌드는 하나 이상의 projects로 구성.
    -   각 project는 하나 이상의 task들로 구성.
        -   task는 어떤 클래스를 컴파일하거나 JAR를 생성하거나 javadoc을 만드는 작업들을 의미.

```
task squid {
  doLast {
    println 'Hello!! Codingsquid'
  }
}

```

-   간단한 squid task를 위와 같이 직접 만들 수 있음. gradle -q squid로 해당 task만 실행해 볼 수 있음.
-   Gradle 스크립트는 DSL이지만, 몇가지 약속을 제외하면 groovy라는 언어가 지닌 강점을 모두 이용할 수 있음.

```
task squid {
    doLast {
        println 'Hello!! Codingsquid'
    }
}
task octopus(dependsOn: squid) {
    doLast {
        println "I'm Octopus"
    }
}

```

-   dependsOn을 사용해서 task간의 의존성을 만들 수 있음. 위 예제의 경우, octopus task를 실행하면 먼저 squid가 실행되고 octopus가 실행됨.

#### DSL

> Domain-Specific Language(DSL)은 특정 도메인에 특화된 비교적 작고 간단한 프로그래밍 언어. 도메인 문제를 해결 할 때, 문제 해결 아이디어와 최대한 유사한 형태의 문법으로 프로그래밍 할 수 있도록 하는 것이 주요 컨셉임. DSL의 반대 개념은 General Purpose Language(GPL)이며 Java나 C++같은 보편적인 언어를 지칭.

#### sourceSet

-   Java 플러그인에는 Source Set이라는 개념이 들어가 있으며, 이는 함께 컴파일과 실행되는 소스 파일들의 그룹을 뜻함.
-   소스 셋에는 자바 소스 파일과 리소스 파일들이 들어감.
-   다른 플러그인들이 그루비나 스칼라 소스를 추가할 수 있음.
-   소스 셋은 컴파일 클래스패스와 런타임 클래스패스와 관련됨.
-   소스 셋의 목적은 소스들를 논리적 그룹으로 묶고 그 목적을 설명하는 데 있음. 통합 테스트용 소스셋, API 인터페이스 클래스들, 구현체 클래스들 형태로 구분 가능.
-   기본 Java Source Set
    -   main : 실제 작동 소스코드. 컴파일해서 JAR 파일로 들어감.
    -   test : 단위 테스트 소스코드. 컴파일해서 JUnit이나 TestNG로 실행.

#### 새로운 sourceSet 생성 후 jar에 묶는 법

sourceSets { main { java { srcDir "src-gen/main/java"  }  }  } jar { baseName =  "${artifactId}" version =  "${versionId}" excludes =  ['**/application.yml'] from sourceSets.main.allSource //sourceSets의 main의 모든 java파일과 resource를 포함 bootJar.enabled =  false jar.enabled =  true  }

#### task에 타입 지정

def queryDslOutput =  file("src-gen/main/java") task generateQueryDSL(type: JavaCompile, group:  "build")  { doFirst {  if  (!queryDslOutput.exists())  { logger.info("Creating `$queryDslOutput` directory")  if  (!queryDslOutput.mkdirs())  {  throw  new  InvalidUserDataException("Unable to create `$queryDslOutput` directory")  }  }  } source = sourceSets.main.java
  classpath = configurations.compile
  options.compilerArgs =  [  "-proc:only",  "-processor",  "com.querydsl.apt.jpa.JPAAnnotationProcessor"  ] destinationDir = queryDslOutput
  options.failOnError =  false  } compileJava { dependsOn generateQueryDSL
  source generateQueryDSL.destinationDir }

-   type을 javaCompile로 지정함으로써 javaCompile task를 수행할 때 같이 수행하게된다. (compileJava dependsOn을 확인)

#### dependency scope

-   compileOnly

> Spring Boot 의 AutoConfiguration 을 살펴보면, 모든 선택적 의존성에 Dependency Optional True 가 붙어 있는 것을 확인할 수 있음. Gradle 에서는 compileOnly 를 사용할 수 있음. 대부분의 선택적 의존성들은 Starter Pack 에 의하여 주입됨.



# References

[https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88](https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88)

<!--stackedit_data:
eyJoaXN0b3J5IjpbNTg4NTIzMzcyLC03ODAzMjI0NSwtOTE4NT
EwMDc5LDIxMjEzMzc0ODIsLTE5NTYwMzQxMzAsLTE2MDE4OTIz
ODYsLTg5MzgxMTk1NCwyMDg2NjI2ODMxLC05MDU1MjQ5NDIsLT
g1MTI4ODc1NSwxOTIwNjgyMzA3XX0=
-->