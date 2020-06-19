# Gradle 

Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that is designed to be flexible enough to build almost any type of software. 

Gradle은 불필요한 작업을 피함으로써 높은 성능을 보입니다. 또한 build cache를 이용할 수 있고, 그 밖에 많은 최적화 기법이 높아 들어 있고, 개발자 팀의 계속해서 Gradle의 성능을 높이기 위해 노력하고 있습니다. 

Gradle은 JVM에 기반을 두고 있습니다. 따라서 JDK(Java Development Kit)이 반드시 필요합니다. 따라서 Java platform에 친숙한 사람에게는 익숙합니다. 그러나 Gradle은 JVM 프로젝트 빌드에만 국한되지 않으며, native 프로젝트 빌드도 지원합니다.

Gradle은 Maven의 Convetions을 가져왔고 일반적인 타입의 프로젝트를 쉽게 빌드할 수 있습니다. 적합한 플러그인을 적용하여 쉽게 빌드가 가능합니다. 


### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#1_gradle_is_a_general_purpose_build_tool)[1. Gradle is a general-purpose build tool](https://docs.gradle.org/current/userguide/what_is_gradle.html#1_gradle_is_a_general_purpose_build_tool)

Gradle allows you to build any software, because it makes few assumptions about what you’re trying to build or how it should be done. The most notable restriction is that dependency management currently only supports Maven- and Ivy-compatible repositories and the filesystem.

This doesn’t mean you have to do a lot of work to create a build. Gradle makes it easy to build common types of project — say Java libraries — by adding a layer of conventions and prebuilt functionality through  [_plugins_](https://docs.gradle.org/current/userguide/plugins.html#plugins). You can even create and publish custom plugins to encapsulate your own conventions and build functionality.

### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#the_core_model_is_based_on_tasks)[2. The core model is based on tasks](https://docs.gradle.org/current/userguide/what_is_gradle.html#the_core_model_is_based_on_tasks)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzMzc4MzcwODYsMTkyMDY4MjMwN119
-->