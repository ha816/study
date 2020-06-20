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
	* 빌드를어떤 프로젝트들이 참가할지 
* Configuration
	* Constructs and configures the task graph for the build and then determines which tasks need to run and in which order, based on the task the user wants to run.
* Execution
	* Runs the tasks selected at the end of the configuration phase.
    
Apache Maven Terminology(용어)에 빗대어 비교해보자면, Gradle의 build phase는 Maven의 phases와는 다릅니다. 

Maven은 phases를 build execution을 다수의 stages로 나누기 위해 사용한다. Maven의 phases를 Gradle의 task graph와 유사한 역할을 하지만, 유연함이 떨어집니다.
Maven의 build lifecycle의 개념은 Gradle’s  [lifecycle tasks](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks) 보다 느슨하게 비슷합니다.

잘 디자인된 build scripts는 명령형 로직보다 선언적 설정으로 구성됩니다.
>Well-designed build scripts consist mostly of  [declarative configuration rather than imperative logic](https://docs.gradle.org/current/userguide/authoring_maintainable_build_scripts.html#sec:avoid_imperative_logic_in_scripts). 


### [4. Gradle is extensible in more ways than one](https://docs.gradle.org/current/userguide/what_is_gradle.html#4_gradle_is_extensible_in_more_ways_than_one)

Gradle의 빌드 로직만 따라서 프로젝트를 만들면 좋겠지만, 그럴일이 별로 없다. 대부분의 build는 커스텀 빌드 로직을 추가할 필요가 있다.  Gradle은 커스텀 빌드 로직을 위한 몇가지 메커니즘을 제공한다.


* [Custom task types](https://docs.gradle.org/current/userguide/custom_tasks.html)
	* When you want the build to do some work that an existing task can’t do, you can simply write your own task type. It’s typically best to put the source file for a custom task type in the  [_buildSrc_](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources)  directory or in a packaged plugin. Then you can use the custom task type just like any of the Gradle-provided ones.
    
* Custom task actions
	* You can attach custom build logic that executes before or after a task via the  [Task.doFirst()](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doFirst(org.gradle.api.Action))  and  [Task.doLast()](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doLast(org.gradle.api.Action))  methods.
    
* [Extra properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties)  on projects and tasks
	* These allows you to add your own properties to a project or task that you can then use from your own custom actions or any other build logic. Extra properties can even be applied to tasks that aren’t explicitly created by you, such as those created by Gradle’s core plugins.
    
-   Custom conventions.
    
    Conventions are a powerful way to simplify builds so that users can understand and use them more easily. This can be seen with builds that use standard project structures and naming conventions, such as  [Java builds](https://docs.gradle.org/current/userguide/building_java_projects.html#building_java_projects). You can write your own plugins that provide conventions — they just need to configure default values for the relevant aspects of a build.
    
* [A custom model](https://guides.gradle.org/implementing-gradle-plugins/#modeling_dsl_like_apis)
	* Gradle allows you to introduce new concepts into a build beyond tasks, files and dependency configurations. You can see this with most language plugins, which add the concept of  [_source sets_](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:java_source_sets)  to a build. Appropriate modeling of a build process can greatly improve a build’s ease of use and its efficiency.

# Configuration


# References

[https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88](https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88)

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4OTMyNDkxMTAsLTE5NTYwMzQxMzAsLT
E2MDE4OTIzODYsLTg5MzgxMTk1NCwyMDg2NjI2ODMxLC05MDU1
MjQ5NDIsLTg1MTI4ODc1NSwxOTIwNjgyMzA3XX0=
-->