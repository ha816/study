# Gradle 

Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that is designed to be flexible enough to build almost any type of software. 

Gradle은 불필요한 작업을 피함으로써 높은 성능을 보입니다. 또한 build cache를 이용할 수 있고, 그 밖에 많은 최적화 기법이 높아 들어 있고, 개발자 팀의 계속해서 Gradle의 성능을 높이기 위해 노력하고 있습니다. 

Gradle은 JVM에 기반을 두고 있습니다. 따라서 JDK(Java Development Kit)이 반드시 필요합니다. 따라서 Java platform에 친숙한 사람에게는 익숙합니다. 그러나 Gradle은 JVM 프로젝트 빌드에만 국한되지 않으며, native 프로젝트 빌드도 지원합니다.

Gradle은 Maven의 Convetions을 가져왔고 일반적인 타입의 프로젝트를 쉽게 빌드할 수 있습니다. 적합한 플러그인을 적용하여 쉽게 빌드가 가능합니다. 


### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#1_gradle_is_a_general_purpose_build_tool)[1. Gradle is a general-purpose build tool](https://docs.gradle.org/current/userguide/what_is_gradle.html#1_gradle_is_a_general_purpose_build_tool)

Gradle allows you to build any software, because it makes few assumptions about what you’re trying to build or how it should be done. The most notable restriction is that dependency management currently only supports Maven- and Ivy-compatible repositories and the filesystem.

This doesn’t mean you have to do a lot of work to create a build. Gradle makes it easy to build common types of project — say Java libraries — by adding a layer of conventions and prebuilt functionality through  [_plugins_](https://docs.gradle.org/current/userguide/plugins.html#plugins). You can even create and publish custom plugins to encapsulate your own conventions and build functionality.

### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#the_core_model_is_based_on_tasks)[2. The core model is based on tasks](https://docs.gradle.org/current/userguide/what_is_gradle.html#the_core_model_is_based_on_tasks)

Gradle models its builds as Directed Acyclic Graphs (DAGs) of tasks (units of work). What this means is that a build essentially configures a set of tasks and wires them together — based on their dependencies — to create that DAG. Once the task graph has been created, Gradle determines which tasks need to be run in which order and then proceeds to execute them.

This diagram shows two example task graphs, one abstract and the other concrete, with the dependencies between the tasks represented as arrows:

![Example task graphs](https://docs.gradle.org/current/userguide/img/task-dag-examples.png)

Figure 1. Two examples of Gradle task graphs

Almost any build process can be modeled as a graph of tasks in this way, which is one of the reasons why Gradle is so flexible. And that task graph can be defined by both plugins and your own build scripts, with tasks linked together via the  [task dependency mechanism](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:task_dependencies).

Tasks themselves consist of:

-   Actions — pieces of work that do something, like copy files or compile source
    
-   Inputs — values, files and directories that the actions use or operate on
    
-   Outputs — files and directories that the actions modify or generate
    

In fact, all of the above are optional depending on what the task needs to do. Some tasks — such as the  [standard lifecycle tasks](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks)  — don’t even have any actions. They simply aggregate multiple tasks together as a convenience.

You choose which task to run. Save time by specifying the task that does what you need, but no more than that. If you just want to run the unit tests, choose the task that does that — typically  `test`. If you want to package an application, most builds have an  `assemble`  task for that.

One last thing: Gradle’s  [incremental build](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:up_to_date_checks)  support is robust and reliable, so keep your builds running fast by avoiding the  `clean`  task unless you actually do want to perform a clean.

### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#3_gradle_has_several_fixed_build_phases)[3. Gradle has several fixed build phases](https://docs.gradle.org/current/userguide/what_is_gradle.html#3_gradle_has_several_fixed_build_phases)

It’s important to understand that Gradle evaluates and executes build scripts in three phases:

1.  Initialization
    
    Sets up the environment for the build and determine which projects will take part in it.
    
2.  Configuration
    
    Constructs and configures the task graph for the build and then determines which tasks need to run and in which order, based on the task the user wants to run.
    
3.  Execution
    
    Runs the tasks selected at the end of the configuration phase.
    

These phases form Gradle’s  [Build Lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html#build_lifecycle).

Comparison to Apache Maven terminology

Gradle’s build phases are not like Maven’s phases. Maven uses its phases to divide the build execution into multiple stages. They serve a similar role to Gradle’s task graph, although less flexibly.

Maven’s concept of a build lifecycle is loosely similar to Gradle’s  [lifecycle tasks](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks).

Well-designed build scripts consist mostly of  [declarative configuration rather than imperative logic](https://docs.gradle.org/current/userguide/authoring_maintainable_build_scripts.html#sec:avoid_imperative_logic_in_scripts). That configuration is understandably evaluated during the configuration phase. Even so, many such builds also have task actions — for example via  `doLast {}`  and  `doFirst {}`  blocks — which are evaluated during the execution phase. This is important because code evaluated during the configuration phase won’t see changes that happen during the execution phase.

Another important aspect of the configuration phase is that everything involved in it is evaluated  _every time the build runs_. That is why it’s best practice to  [avoid expensive work during the configuration phase](https://docs.gradle.org/current/userguide/authoring_maintainable_build_scripts.html#sec:minimize_logic_executed_configuration_phase).  [Build scans](https://scans.gradle.com/)  can help you identify such hotspots, among other things.

### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#4_gradle_is_extensible_in_more_ways_than_one)[4. Gradle is extensible in more ways than one](https://docs.gradle.org/current/userguide/what_is_gradle.html#4_gradle_is_extensible_in_more_ways_than_one)

It would be great if you could build your project using only the build logic bundled with Gradle, but that’s rarely possible. Most builds have some special requirements that mean you need to add custom build logic.

Gradle provides several mechanisms that allow you to extend it, such as:

-   [Custom task types](https://docs.gradle.org/current/userguide/custom_tasks.html).
    
    When you want the build to do some work that an existing task can’t do, you can simply write your own task type. It’s typically best to put the source file for a custom task type in the  [_buildSrc_](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources)  directory or in a packaged plugin. Then you can use the custom task type just like any of the Gradle-provided ones.
    
-   Custom task actions.
    
    You can attach custom build logic that executes before or after a task via the  [Task.doFirst()](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doFirst(org.gradle.api.Action))  and  [Task.doLast()](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doLast(org.gradle.api.Action))  methods.
    
-   [Extra properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties)  on projects and tasks.
    
    These allows you to add your own properties to a project or task that you can then use from your own custom actions or any other build logic. Extra properties can even be applied to tasks that aren’t explicitly created by you, such as those created by Gradle’s core plugins.
    
-   Custom conventions.
    
    Conventions are a powerful way to simplify builds so that users can understand and use them more easily. This can be seen with builds that use standard project structures and naming conventions, such as  [Java builds](https://docs.gradle.org/current/userguide/building_java_projects.html#building_java_projects). You can write your own plugins that provide conventions — they just need to configure default values for the relevant aspects of a build.
    
-   [A custom model](https://guides.gradle.org/implementing-gradle-plugins/#modeling_dsl_like_apis).
    
    Gradle allows you to introduce new concepts into a build beyond tasks, files and dependency configurations. You can see this with most language plugins, which add the concept of  [_source sets_](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:java_source_sets)  to a build. Appropriate modeling of a build process can greatly improve a build’s ease of use and its efficiency.
    

### [](https://docs.gradle.org/current/userguide/what_is_gradle.html#5_build_scripts_operate_against_an_api)[5. Build scripts operate against an API](https://docs.gradle.org/current/userguide/what_is_gradle.html#5_build_scripts_operate_against_an_api)

It’s easy to view Gradle’s build scripts as executable code, because that’s what they are. But that’s an implementation detail: well-designed build scripts describe  _what_  steps are needed to build the software, not  _how_  those steps should do the work. That’s a job for custom task types and plugins.

There is a common misconception that Gradle’s power and flexibility come from the fact that its build scripts are code. This couldn’t be further from the truth. It’s the underlying model and API that provide the power. As we recommend in our best practices, you should  [avoid putting much, if any, imperative logic in your build scripts](https://docs.gradle.org/current/userguide/authoring_maintainable_build_scripts.html#sec:avoid_imperative_logic_in_scripts).

Yet there is one area in which it is useful to view a build script as executable code: in understanding how the syntax of the build script maps to Gradle’s API. The API documentation — formed of the  [Groovy DSL Reference](https://docs.gradle.org/current/dsl/)  and the  [Javadocs](https://docs.gradle.org/current/javadoc/)  — lists methods and properties, and refers to closures and actions. What do these mean within the context of a build script? Check out the  [Groovy Build Script Primer](https://docs.gradle.org/current/userguide/groovy_build_script_primer.html#groovy_build_script_primer)  to learn the answer to that question so that you can make effective use of the API documentation.

As Gradle runs on the JVM, build scripts can also use the standard  [Java API](https://docs.oracle.com/javase/8/docs/api). Groovy build scripts can additionally use the Groovy APIs, while Kotlin build scripts can use the Kotlin ones.
> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNTU4MDcyOTEsMTkyMDY4MjMwN119
-->