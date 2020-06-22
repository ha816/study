# Overview

Gradle은 오픈 소스 빌드 자동화 도구입니다. 

>Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that is designed to be flexible enough to build almost any type of software. 

* Gradle을 활용하면 어떤 소프트웨어도 빌드가 가능합니다. 이는 어떤 프로젝트를 어떤식으로 빌드하든 Gradle에서 강제하는 제약이 거의 없기 때문입니다. 

* Gradle은 빌드 성능을 높이기 위한 많은 최적화 기법을 사용하고 있습니다. 대표적으로  불필요한 빌드 작업을 줄이기 위해 build cache를 활용합니다. 

* Gradle은 [Maven Conventions](https://maven.apache.org/maven-conventions.html)을 따릅니다. 

* Gradle은 Java 기반입니다. 따라서 사용하기 위해 JDK(Java Development Kit)가 반드시 필요합니다. 그러나 Gradle은 JVM 프로젝트 빌드에만 국한되지 않고 다양한 소프트웨어를 빌드할 수 있습니다.  당연히 플러그인도 빌드에 활용할 수 있습니다.

* Gradle은 주로 Groovy로 작성된 빌드 스크립트로 빌드를 하게 됩니다. Groovy는 JVM에서 작동하는 동적 스크립트 언어입니다. 문법이 자바 문법과 아주 비슷하여 배우기에 큰 어려움이 없습니다.

* Gradle이 지원하는 의존성 관리는 [Maven Dependency Management](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)와 Ivy-compatible repositories가 있습니다.

# Concepts

## Projects & Tasks

모든 Gradle 빌드는 최소 하나 이상의 프로젝트들로 만들어집니다. 프로젝트가 무엇을 나타내는지는 Gradle로 무엇을 하느냐에 달려 있습니다. 구성에 따라 다르지만, 다수의 프로젝트를 동시에 빌드하
는 것도 가능합니다.

모든 Gradle 빌드에 최소 하나 이상의 프로젝트가 존재하는 것 처럼, 각 프로젝트는 최소 하나 이상의 task로 구성됩니다. 

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


## Task Graph

Gradle은 task 집단으로 구성된 Directed Acyclic Graphs(DAGs)로 전체 빌드 과정을 모델링합니다. 즉 하나의 빌드는 반드시 tasks와 tasks간의 의존성을 나타내는 DAG를 만든다는 의미입니다. 그래프가 생성되면, Gradle은 어떤 task가 어떤 순서로 동작해야 하는지 판단하고 작업을 수행합니다.

![Example task graphs](https://docs.gradle.org/current/userguide/img/task-dag-examples.png)

위 두 그림은 task 그래프의 예제로 화살은 task간의 의존성을 나타냅니다. 왼쪽 그림은 추상화된 일반적 task 그래프를 나타냅니다. 오른쪽은 실제 표준 자바를 빌드하는 과정을 표현한 task 그래프입니다.

대부분의 어떤 빌드 과정도 task 그래프로 모델링될 수 있습니다. 그리고 이것이 Gradle이 유연해질 수 있는 이유입니다. task 그래프는 외부 플러그인 또는 Gradle의 [build script](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)으로도 모델링 가능합니다.

## Build Lifecycle

Gradle은 Initialization, Configuration, Execution의 세 단계로 build scripts를 평가하고 실행합니다. 그리고 이 세 가지 단계가 Gradle의 [Build Lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html#build_lifecycle)의 핵심입니다.

* Initialization
	* 빌드를 위한 환경을 구축하고 어떤 프로젝트들이 참가할지 판별하는 단계.
* Configuration
	* 빌드를 위한 task 그래프를 만들고 조정한 뒤, 사용자가 원하는 task에 따라 어떤 순서로 어떤 tasks가 필요한지 판단하는 단계.
* Execution
	* Configuration 단계가 끝난 후, 선택된 tasks를 수행하는 단계.
   
## Build Script

Gradle의 빌드 스크립트는 실제 실행 코드입니다. 이렇듯 빌드 스크립트가 코드인 사실 덕분에 Gradle의 유연함이 온다고 생각할 수 있는데, 이는 사실이 아닙니다.

어떻게 빌드 스크립트가 [Gradle's API](https://docs.gradle.org/current/javadoc/index.html)에 대응하는지 구문을 이해하는 것은 중요합니다. Gradle's API는 [Groovy DSL Reference](https://docs.gradle.org/current/dsl/)과 [Javadocs](https://docs.gradle.org/current/javadoc/)로 작성 되어있습니다.

Gradle은  JVM에서 동작하기 때문에, 빌드 스크릷트들은 표준 [Java API](https://docs.oracle.com/javase/8/docs/api)를 사용할 수 있습니다.
먄약 Groovy 빌드 스크립트를 사용한다면 Groovy API들도 추가적으로 사용 가능합니다. 반면 Kotlin 빌드 스크립트는 Kotlin것만 사용 가능합니다. 

# References

[https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88](https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88)

<!--stackedit_data:
eyJoaXN0b3J5IjpbNTQ3MTAzMTkxLDc4OTY3Njg2LC05ODQzNj
E3NTMsLTE3OTk3OTI1ODUsMTk5MzU4MDQ1Nl19
-->