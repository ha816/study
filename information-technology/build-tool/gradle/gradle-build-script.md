# Overview

빌드 스크립트를 실제 어떻게 만드는지 알아봅시다. 
아래 예제는 [Gradle User Guide](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)를 가져와 정리하였습니다.

# [Hello world](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:hello_world)

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




# [Task dependencies](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:task_dependencies)

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

# [Manipulating existing tasks](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:manipulating_existing_tasks)

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

# [Groovy DSL shortcut notations](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:shortcut_notations)

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

# [Extra task properties](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:extra_task_properties)

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


# [Using methods](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:using_methods)

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

# [Default tasks](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:default_tasks)

Gradle은 정의된 다른 tasks가 없다면 디폴트 tasks 정의하도록 허락합니다. 

```groovy
defaultTasks 'clean', 'run'

task clean {
    doLast {
        println 'Default Cleaning!'
    }
}

task run {
    doLast {
        println 'Default Running!'
    }
}

task other {
    doLast {
        println "I'm not a default task!"
    }
}
```

> gradle -q
> Default Cleaning!
> Default Running!

`gradle -q` 특정 task를 파라미터로 넣지 않아도 디폴트 task를  수행합니다. 사실 `gradle clean run`로 빌드를 실행한 것과 마찬가지입니다. multi-project 빌드에선 모든 하위 프로젝트가 디폴트 task를 가질 수 있습니다. 만약 하위 프로젝트가 정의된 디폴트 tasks가 없다면, 부모 프로젝트에 정의된 디폴트 task를 사용하게 됩니다. 부모 프로젝트에 디폴트 task에도 없다면 실행하지 않습니다. 

# [Configure by DAG](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#configure-by-dag)

Gradle의 configuration phase가 끝나면, 수행해야할 모든 task를 알게 됩니다. Gradle은 이 정보를 사용하기 위해 hook을 제공합니다. 하나의 예로, tasks중에서 실행되어야할 release task를 확인할 수 있습니다. 덕분에 몇 가지 변수에 다른 값을 할당할 수도 있습니다. 

아래 예제에서는`version`  변수에 따라 `distribution`과 `release`  tasks의 다른 수행 결과를 보여줍니다. 

```groovy
task distribution {
    doLast {
        println "We build the zip with version=$version"
    }
}

task release {
    dependsOn 'distribution'
    doLast {
        println 'We release now'
    }
}

gradle.taskGraph.whenReady { taskGraph ->
    if (taskGraph.hasTask(":release")) {
        version = '1.0'
    } else {
        version = '1.0-SNAPSHOT'
    }
}
```
> gradle -q distribution
We build the zip with version=1.0-SNAPSHOT

> gradle -q release
We build the zip with version=1.0
We release now

중요한 점은 `whenReady`를 사용하면 release task가 수행되기 전에 적용된다는 점입니다. 심지어 gradle에 인자로 넘어간 release task가 넘어가지 않더라도 적용됩니다. 즉 언제나 적용됩니다.

이 예제는 `version`  변수가 execution에서만 읽히기 때문에 제대로 동작합니다. 따라서 실제 운영 빌드에선 어떤 곳에서도 configuration 과정에서 이 변수가 읽히는 일이 없도록 해야 합니다. 제대로 하지 않으면, 빌드 과정의 configuration과 execution 사이에서 하나의 변수 값이 서로 달라지는 불상사가 발생할지도 모릅니다. 

# [External dependencies for the build script](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:build_script_external_dependencies)

만약 빌드 스크립트가 외부 라이브러리들을 사용해야 한다면, 스크립트의 classpath에 추가하면 된다. `buildscript()`  메서드를 사용하면, classpath에 정의된 블럭을 보고 외부 라이브러리를 빌드한다. 

```groovy
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath group: 'commons-codec', name: 'commons-codec', version: '1.2'
    }
}
```

`buildscript()` 메서드로 전해진 블럭은 [ScriptHandler](https://docs.gradle.org/current/javadoc/org/gradle/api/initialization/dsl/ScriptHandler.html) 를 조정합니다. `classpath`  configuration에 의존성을 추가함으로 빌드 스크립트 classpath를 선언했습니다. 같은 방법으로, java compile classpath도 선언할 수 있습니다.

프로젝트 의존성을 제외한 어떤 [dependency types](https://docs.gradle.org/current/userguide/declaring_dependencies.html#sec:dependency-types)도 사용가능합니다.

아래 예제는 위의 예제에서 빌드 스크립트 classpath의 클래스를 사용합니다. 

```groovy
import org.apache.commons.codec.binary.Base64

buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath group: 'commons-codec', name: 'commons-codec', version: '1.2'
    }
}

task encode {
    doLast {
        def byte[] encodedString = new Base64().encode('hello world\n'.getBytes())
        println new String(encodedString)
    }
}
```

> gradle -q encode
> aGVsbG8gd29ybGQK

multi-project 빌드에서, 한 프로젝트의 `buildscript()`로 선언된 의존성을 이것의 하위 프로젝트를 빌드 스크립트에서 사용이 가능합니다. 

플러그인을 이용한 방법은  [Using Gradle Plugins](https://docs.gradle.org/current/userguide/plugins.html#plugins) 을 보도록 합시다.


# references

[https://docs.gradle.org/current/userguide/tutorial_using_tasks.html](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)

[https://dynaticy.tistory.com/entry/Gradle-UserGuide-%EB%8F%84%EC%A0%84%EA%B8%B0-6-Build-Script-Basics?category=536334](https://dynaticy.tistory.com/entry/Gradle-UserGuide-%EB%8F%84%EC%A0%84%EA%B8%B0-6-Build-Script-Basics?category=536334)

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI1OTgyNjA4Myw3NjM5Njk4MTcsNDk1Nj
I0Mzc4LC0xODgxODcxNjA4LC0xNjc0NjUxNDg3LDE0MDUxODk5
MTAsNzI0ODk4NzUzLDIxMzQ0NTAxNjEsLTE3ODUwMzMzOTAsMj
EzMjg3MDcyN119
-->