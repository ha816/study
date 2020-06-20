# Gradle

Gradle은 오픈 소스 빌드 자동화 도구입니다. 

>Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that is designed to be flexible enough to build almost any type of software. 

Gradle은 불필요한 작업을 피함하여 좋은 성능을 보입니다. 또한 build cache를 이용할 수 있고, 그 밖에 많은 최적화 기법이 높아 들어 있고, 개발자 팀의 계속해서 Gradle의 성능을 높이기 위해 노력하고 있습니다. 

Gradle은 JVM에 기반을 두고 있습니다. 따라서 JDK(Java Development Kit)이 반드시 필요합니다. 따라서 Java platform에 친숙한 사람에게는 익숙합니다. 그러나 Gradle은 JVM 프로젝트 빌드에만 국한되지 않으며, native 프로젝트 빌드도 지원합니다.

Gradle은 Maven의 Convetions을 가져왔고 일반적인 타입의 프로젝트를 쉽게 빌드할 수 있습니다. 적합한 플러그인을 적용하여 쉽게 빌드가 가능합니다. 


### [1. Gradle is a general-purpose build tool](https://docs.gradle.org/current/userguide/what_is_gradle.html#1_gradle_is_a_general_purpose_build_tool)

Gradle을 사용하는데 큰 제약은 없지만, 가장 주의할 제약은 의존성 관리이다. 의존성 관리는 현재 Maven의 dependency management와 Ivy-compatible 만 지원한다.


### [2. The core model is based on tasks](https://docs.gradle.org/current/userguide/what_is_gradle.html#the_core_model_is_based_on_tasks)

Gradle은 tasks로 구성된 Directed Acyclic Graphs (DAGs)로 빌드를 모델링한다. 즉 하나의 빌드는 필수적으로 작업들과 그에 연관된 의존성으로 DAG를 만들어야 한다는 의미다. 한번 그래프가 생성되면, Gradle은 어떤 task가 어떤 순서로 동작해야 하는지 판단하고 그것들을 수행한다. 

![Example task graphs](https://docs.gradle.org/current/userguide/img/task-dag-examples.png)

위 두 그림은 task graphs의 예제입니다. 화살은 task간의 의존성을 나타냅니다.
왼쪽 그림은 추상적이고 오른쪽은 실제 구현입니다.

대부분 어떤 빌드 과정도 이런식으로 task 그래프로 모델링될 수 있습니다. 그리고 이것이 Gradle이 유연해질 수 있는 이유 입니다. 그리고 task 그래프는 플러그 인과 독자적인 build 스크립트인 [task dependency mechanism](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:task_dependencies)와 연계되어 더 정의될 수 있습니다. 


Tasks는 아래 Actions, Inputs, Outputs로 구성됩니다.
* Actions
	* pieces of work that do something, like copy files or compile source
* Inputs
	* values, files and directories that the actions use or operate on
* Outputs
	* files and directories that the actions modify or generate

사실, 위의 모든 것은 task가 해야할것에 따라 선택적이다. 예를 들어 [standard lifecycle tasks](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks) 과 같은 몇몇의 tasks는 어떤 actions도 가지지 않는다. 단지 편리하게 다수의 tasks를 묶는 역할만을 한다. 

필요로하는 특정 task를 골라 돌리면, task를 정의하기 위한 시간을 아낄 수 있다. 만약 unit test를 돌려보고 싶다면, `test` task를 찾으면 됩니다. 만약 애플리케이션을 package하고 싶다면, 일반적으로 `assemble`을 수행하면 됩니다.

Gradle’s [incremental build](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:up_to_date_checks) support을 이용하면 `clean` task를 하지 않아도 빌드 과정을 빠르게 합니다. 

### [3. Gradle has several fixed build phases](https://docs.gradle.org/current/userguide/what_is_gradle.html#3_gradle_has_several_fixed_build_phases)

Gradle이  Initialization, Configuration, Execution의 세 단계로 build scripts를 평가하고 실행하는 것을 이해하는것은 매우 중요합니다. 세 단계는 Gradle’s  [Build Lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html#build_lifecycle)
을 이룹니다. 

* Initialization
	* Sets up the environment for the build and determine which projects will take part in it.
* Configuration
	* Constructs and configures the task graph for the build and then determines which tasks need to run and in which order, based on the task the user wants to run.
* Execution
	* Runs the tasks selected at the end of the configuration phase.
    
Apache Maven terminology(용어)에 빗대어 보자면, Gradle의 build phase는 Maven’s phases와는 다릅니다. Maven은 phases를 build execution을 다수의 stages로 나누기 위해 사용한다. Maven의 phases를 Gradle의 task graph와 유사한 역할을 하지만, 유연함이 떨어집니다.
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


[https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88](https://effectivesquid.tistory.com/entry/Gradle-%EB%B9%8C%EB%93%9C%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B8%B0%EC%B4%88)

<!--stackedit_data:
eyJoaXN0b3J5IjpbNzg5MTQ5NzI2LC04OTM4MTE5NTQsMjA4Nj
YyNjgzMSwtOTA1NTI0OTQyLC04NTEyODg3NTUsMTkyMDY4MjMw
N119
-->