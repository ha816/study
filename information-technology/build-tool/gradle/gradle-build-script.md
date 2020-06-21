# Overview

스크립트를 실제 어떻게 사용하는지 알아보는 장입니다. 

## [Hello world](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:hello_world)

기본적으로 Gradle의 빌드 과정에서 `gradle` 명령어를 사용하게 됩니다. `gradle` 명령은 `build.gradle` 파일을 현재 디렉토리에서 찾습니다. `build.gradle`을 빌드 스크립트라고 부르는데, 보다 엄밀히는 빌드 설정 스크립트(build configuration script)라 합니다. 
빌드 스크립트는 프로젝트와 프로젝트가 가지는 task를 정의합니다.

``` groovy
// In build.gradle 
task hello {
    doLast { // action
        println 'Hello world!'
    }
}
```

위 빌드 스크립트를 실행해보고 싶다면, 파일이 존재하는 디렉토리로 이동해서 `gradle -q hello`를 실행하면 됩니다. 그런데 여기서 `-q` 는 무엇을 할까요? 
`-q`는 옵션으로, Gradle의 로그 메세지를 나타냅니다. 이걸 써야만 tasks의 아웃풋만이 결과로 나타납니다. 안쓰고 싶다면 `-q` 옵션을 쓰지 않아도 됩니다. [Logging](https://docs.gradle.org/current/userguide/logging.html#logging)

`gradle -q hello`을 실행하면, 빌드 스크립트에 정의된 hello란 task를 찾아 action을 추가 합니다. Gradle은 hello task를 실행하며, 추가된 action을 순차적으로 하나씩 처리합니다. action은 단지 한 블럭으로 실행할 코드를 가지고 있습니다. 

## [Task dependencies](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:task_dependencies)

다른 tasks 위에 또 다른 tasks를 선언할 수 있습니다. 아래 예제는 intro task가 hello task에 의지합니다. 

```groovy
task hello {
    doLast {
        println 'Hello world!'
    }
}
task intro {
    dependsOn hello
    doLast {
        println "I'm Gradle"
    }
}
```

명령어 `gradle -q intro`를 실행 하면 아래 결과가 나타납니다.

>Hello world!
>I'm Gradle

의존성을 추가하기 할때는, 대응하는 task가 실제로 존재하지 않아도 괜찮습니다. 

```groovy
task taskX {
    dependsOn 'taskY'
    doLast {
        println 'taskX'
    }
}
task taskY {
    doLast {
        println 'taskY'
    }
}
```

> taskY
> taskX

`taskX` 에서  `taskY`에 대한 의존성은 `taskY`가 정의되기 전에 선언되었더라도 잘 동작합니다. 이것은 multi-project 빌드에 매우 중요한 특성입니다. 

한 가지 주의할 점은 아직 정의 되지 않은 task를 참조할때는,  [shortcut notation](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:shortcut_notati)을 사용하면 안됩니다. 

## [Dynamic tasks](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:dynamic_tasks)

Groovy의 장점은 task가 무엇을 하는지 정의하는 것을 동적으로 할 수 있다는 점입니다. 예를 들어, 동적으로 task를 생성할 수 있습니다.

```groovy
4.times { counter ->
    task "task$counter" {
        doLast {
            println "I'm task number $counter"
        }
    }
}
```

> gradle -q task1
I'm task number 1

## [Manipulating existing tasks](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:manipulating_existing_tasks)

한번 tasks가 만들어지고 API를 통해서 접근가능하다면, API를 통해서 동적으로 의존성을 추가 가능합니다. 
```groovy
4.times { counter ->
    task "task$counter" {
        doLast {
            println "I'm task number $counter"
        }
    }
}
task0.dependsOn task2, task3 // 의존성 동적 추가
```
> I'm task number 2
> I'm task number 3
> I'm task number 0

의존성 추가가 아니라 존재하는 task에 행동도 추가할 수 있습니다. 

```groovy
task hello {
    doLast {
        println 'Hello Earth'
    }
}
hello.doFirst {
    println 'Hello Venus'
}
hello.configure {
    doLast {
        println 'Hello Mars'
    }
}
hello.configure {
    doLast {
        println 'Hello Jupiter'
    }
}
```

> gradle -q hello
Hello Venus
Hello Earth
Hello Mars
Hello Jupiter

`doFirst`와 `doLast`은 여러번 실행될 수 있다. 이것들은 taks action 리스트의 처음이나 끝에 action을 추가합니다. task가 실행되면, action 리스트의 action은 순서대로 실행됩니다. 

## [Groovy DSL shortcut notations](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:shortcut_notations)

존재하는 task에서 각 속성을 쉽게 사용 가능합니다. 
```groovy
task hello {
    doLast {
        println 'Hello world!'
    }
}
hello.doLast {
    println "Greetings from the $hello.name task." // hello task의 name 속성
}
```

> gradle -q hello
> Hello world!
> Greetings from the hello task.

## [Extra task properties](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:extra_task_properties)

task의 속성에 특정한 속성을 추가할 수 있습니다. 

```groovy
task myTask {
    ext.myProperty = "myValue"
}

task printTaskProperties {
    doLast {
        println myTask.myProperty
    }
}
```

> gradle -q printTaskProperties
myValue


## [Using methods](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:using_methods)

빌드 로직을 만드는 첫 번째 단계는 사용될 메서드를 추출하는것 입니다. 예제는 아주 간단하지만 보다 자세한 사항을 보고 싶다면 [Organizing Gradle Projects](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#organizing_gradle_projects)을 참조합시다. 


```groovy
task checksum {
    doLast {
        fileList('./antLoadfileResources').each { File file ->
            ant.checksum(file: file, property: "cs_$file.name")
            println "$file.name Checksum: ${ant.properties["cs_$file.name"]}"
        }
    }
}

task loadfile {
    doLast {
        fileList('./antLoadfileResources').each { File file ->
            ant.loadfile(srcFile: file, property: file.name)
            println "I'm fond of $file.name"
        }
    }
}

File[] fileList(String dir) {
    file(dir).listFiles({file -> file.isFile() } as FileFilter).sort()
}
```

> gradle -q loadfile
I'm fond of agile.manifesto.txt
I'm fond of gradle.manifesto.txt


## [](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:extra_task_properties)[Extra task properties](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:extra_task_properties)

You can add your own properties to a task. To add a property named  `myProperty`, set  `ext.myProperty`  to an initial value. From that point on, the property can be read and set like a predefined task property.

Example 11. Adding extra properties to a task

`Groovy``Kotlin`

build.gradle

```groovy
task myTask {
    ext.myProperty = "myValue"
}

task printTaskProperties {
    doLast {
        println myTask.myProperty
    }
}
```

Output of  **`gradle -q printTaskProperties`**

> gradle -q printTaskProperties
myValue

Extra properties aren’t limited to tasks. You can read more about them in  [Extra properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties).


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

# references

[https://docs.gradle.org/current/userguide/tutorial_using_tasks.html](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)

[https://dynaticy.tistory.com/entry/Gradle-UserGuide-%EB%8F%84%EC%A0%84%EA%B8%B0-6-Build-Script-Basics?category=536334](https://dynaticy.tistory.com/entry/Gradle-UserGuide-%EB%8F%84%EC%A0%84%EA%B8%B0-6-Build-Script-Basics?category=536334)



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjEzNDQ1MDE2MSwtMTc4NTAzMzM5MCwyMT
MyODcwNzI3XX0=
-->