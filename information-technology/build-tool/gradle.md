# Gradle 

Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that is designed to be flexible enough to build almost any type of software. 

Gradle은 불필요한 작업을 피함으로써 높은 성능을 보입니다. 또한 build cache를 이용할 수 있고, 그 밖에 많은 최적화 기법이 높아 들어 있고, 개발자 팀의 계속해서 Gradle의 성능을 높이기 위해 노력하고 있습니다. 

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

사실, 위의 모든 것은 task가 해야할

In fact, all of the above are optional depending on what the task needs to do. Some tasks — such as the  [standard lifecycle tasks](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks)  — don’t even have any actions. They simply aggregate multiple tasks together as a convenience.

You choose which task to run. Save time by specifying the task that does what you need, but no more than that. If you just want to run the unit tests, choose the task that does that — typically  `test`. If you want to package an application, most builds have an  `assemble`  task for that.

One last thing: Gradle’s  [incremental build](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:up_to_date_checks)  support is robust and reliable, so keep your builds running fast by avoiding the  `clean`  task unless you actually do want to perform a clean.

### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#3_gradle_has_several_fixed_build_phases)[3. Gradle has several fixed build phases](https://docs.gradle.org/current/userguide/what_is_gradle.html#3_gradle_has_several_fixed_build_phases)

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNTgxNDAxNTQsMTkyMDY4MjMwN119
-->