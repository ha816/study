# Maven(메이븐)

사용하기 좋은 애플리케이션을 만드는 데는 자바 소스 코드를 잘 작성하는 것 이외에도 많은 라이브러리, 플러그인이 필요하다. 한 애플리케이션을 동작하는데 필요한 모든 자원 관리하고 빌드하는 도구의 필요성은 꾸준히 있었다. 

>Maven이란?

**한 프로젝트 전체를 관리하는 빌드 도구**로, 자바 프로젝트를 컴파일, 테스트, 배포하는데 사용된다. 애플리케이션이 구동할때 필요한 자원들을 가져와 여러 빌드 작업을 수행한다. 

>Maven 의존성?

복잡합 애플리케이션은 JUnit, 아파치 commons, Guava등 여러 라이브러리에 의존성을 가지고 있다. 메이븐은 의존성을 정의할 수 있고, 인터넷 상의 www.maven.org 같은 저장소나 Artifactory, Nexus 등을 이용해서 회사 내부에 설치한 저장소에서 필요한 JAR 파일을 직접 다운로드할 수 있다.

>Maven Plugin?

메이븐의 플러그인 시스템은 빌드시 특별한 연산 추가를 가능하게 한다. 의존성 설정과 비슷하게 이 플러그인들은 원격으로 제공되며, 메이븐은 빌드할 때 이들을 찾을 수 있다. 또한 플러그인이 제공하는 연산이 무엇이든 수행할 수 있다. 

## 메이븐 빌드의 기본 디렉터리 구조

![enter image description here](https://p7.hiclipart.com/preview/980/890/407/apache-maven-convention-over-configuration-apache-ant-directory-structure-coc.jpg)

모든 제품 패키지 구조는 src/main/java 디렉터리 하위에 있다. 이 구조의 모든 클래스는 실행할때 클래스 패스 상에 있을 것이다. 

클래스 패스에 다른 파일들도 넣을 수 있다. 이 파일들은 src/main/resources 디렉터리 안에 있다. 여기에는 일반적으로 설정 파일들이 들어간다. 스프링 애플리케이션 컨텍스트의 설정 뿐만 아니라 모든 properties 파일이 포함될 수 있다. 

test 패키지 하위는 테스트용 설정 같은 테스트 코드로 추가할 수 있으며, 이러한 코드는 test/main/java와 test/main/resources에 두면 된다. 메이븐이 빌드의 일부로 어떤 테스트를 실행할때, 이경로에 있는 클래스와 파일들도 클래스 패스에 추가된다. 단 분리된 상태로 유지되며 생산 배포용으로 빌드된 결과물에는 포함되지 않는다. 

## 메이븐 빌드

메이븐 빌드의 정의는 pom.xml에 설정되는데, 이 파일은 프로젝트의 루트 디렉터리에 있다. 메이븐은 이 프로젝트를 빌드하는데 필요한 정보를 pom.xml을 보고 얻는다.
```
<project>
	<modelVersion>4.0.0</modelVersion>
	<groupId>come.wiley</groupdId>
	<artifactId>come.wiley</artifactId>
	<version>come.wiley</version>
	<packaging>jar</packaging>

	<dependencies>
		<dependency>
			<groupId>commons-io<groupId>
			<artifactId>commons-io<artifactId>
			<version>1.4</version>
		</dependency>
		<dependency>
			<groupId>org.springframework<groupId>
			<artifactId>spring-core<artifactId>
			<version>3.0.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>jnit<groupId>
			<artifactId>jnit<artifactId>
			<version>4.8.2</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.apached.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```
POM은 크게 **프로젝트 정의, 빌드용 의존성,  빌드 전용 플러그인** 세가지 영역으로 구분할 수 있다. 

프로젝트 정의는 프로젝트 태그 바로 밑에 정의하는 modelVersion, groupId, version, packaging 등이 된다. 
groupId는 대게 회사 이름으로 패키지 명명 규칙을 따라간다. artifactId는 애플리케이션 이름을 말한다. 이를 코디네이트라고 하거나 임시 참조라고 한다. 

빌드용 의존성에는 dependencies 태그 안에 정의 된다. 빌드를 위해 필요로 하는 의존 라이브러리를 모두 정의할 수 있다. 여기에는 인트라넷 저장소에 이용할 수 있는 회사가 만든 다른 라이브러리일 수도 있고 서드파티 라이브러리가 될 수 도 있다. 

특정 빌드 전용 플러그인은 build 태그로 관여하는데 package 생명 주기 단계에서 어떤 형태의 결과물을 빌드해야 하는지 알려준다. 

## 메이븐 빌드의 생명주기 

>Maven Goal?

애플리케이션 실행을 위한 특정 단위를 골(Goal)이라고 한다. 골에는 compile, test, install 등이 있다.  각 골은 이전 상태에 의존하며 어떤 이유에서든 특정 골을 통한 작업에 문제가 발생하면 전체 빌드가 실패할수 밖에 없다. 

clean
: target 디렉터리를 지워서 이전에 빌드된 파일들을 없앤다. 이는 mvn clean 같은 빌드 상태를 명시해서 초기화하거나 항상 실행되도록 POM을 정의하지 않으면 다음 상태가 되기 전에는 실행하지 않는다.
  
validate
: pom.xml 파일이 메이븐의 빌드 파일용 XML을 따르는지 확인한다. pom.xml 파일이 올바른 검증에 실패한다면 빌드는 메이븐 애플리케이션 초기화를 하기 전에 실패한다.

compile
: 필요한 의존성 전체를 가져온 후, 코드를 컴파일하고, src/main/java의 모든 클래스 파일을 target/classes 디렉터리에 빌드한다. 

test
: src/main/test 디렉터리에 있는 테스트용 클래스들을 컴파일하고 모든 단위테스트 또는 통합 테스트용 코드를 실행한다. 실패하는 테스트가 있으면 기본적으로 전체 빌드가 실패한다.

package
: test 상태가 성공적으로 실행된 다음에 실행되며 WAR나 JAR파일 같은 결과물을 생성한다. 파일은 target 디렉터리 루트에 저장되는데, 독립적으로 실행가능한 파일이 아니다. 왜냐하면 기본적으로 모든 의존 라이브러리를 내장하지 않고 있고 의존성은 여전히 클래스 패스에 포함되어 있어야 하기 때문이다. 참고로 pom.xml의 packaging 태그로 결과물 package 종류를 정할 수 있다. 
```
<packaging>war</packaging>
``` 
install
: 빌드된 결과물들을 내부 Maven 저장소로 보낸다. 저장소는 대게 `HOME/.m2/repository` 디렉터리에 위치한다. 내부적으로 실행되는 메이븐 빌드가 하나 이상 있다면 새로운 결과물은 해당 빌드를 이용할 수 있다. 

deploy
: 메이븐 빌드의 마지막 단계이다. 대게 마무리된 결과물을 배포하는 장소를 정의하거나 배포 장소에 배포를 한다. 배포 장소는 일반적으로 Artifactory나 Nexus 같은 결과물 저장소다. 이러한 저장소들은 빌드를 위한 의존 결과물을 다운로드할 수 있는 전형적인 공간으로 때때로 임시 공간의 역할도 한다.

## 결과물 애플리케이션간 공유

재사용할 수 있는 컴포넌트가 만들어졌다면 여러 프로젝트에서 해당 컴포넌트를 재사용하려 할 것이다. 회사용으로 설정된 메이븐 저장소가 있다면 POM이 의존성을 참조할때 이 저장소를 확인하도록 하고, 성공한 빌드는 저장소에 저장해둬야 한다. 
```
<distributionManagement>
	<repository>
		<id>deployment</id>
		<name>Internal Releases</name>
		<url>http://inter.../releases</url>
	</repository>
</distributionManagement>
```
repository와  snapshotRepository 태그가 있는데 하나는 배포용이고 하나는 스냅샷이다.  version 태그에 `-SNAPSHOT`이라는 접미사를 포함하면 이 결과물이 여전히 개발 중이고 안정화되지 않았다는 것을 말한다. deploy 골을 실행할때 `-SNAPSHOT` 태그가 붙어 있으면 스냅샷 빌드로 snapshotRepository 태그 안에 있는 URL 주소로 배포될 것이다. `-SNAPSHOT` 접미사가 제거된 안정된 버전은 repository 태그 안에 URL에 배포될 것이다.  

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjA1MzYzNzIzXX0=
-->