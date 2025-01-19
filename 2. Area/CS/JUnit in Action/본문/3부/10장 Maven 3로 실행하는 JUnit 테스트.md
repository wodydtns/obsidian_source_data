>[!important]
>관습적 사고는 생각하는 고통에서 우리를 보호해준다
>존 케네스 갤브레이스(John Kenneth Galbraith)

## 10.1 Maven 프로젝트 만들기
- pom.xml
```XML
<?xml version="1.0"?>
<!--
    Licensed to the Apache Software Foundation (ASF) under one or more
    contributor license agreements. See the NOTICE file distributed with
    this work for additional information regarding copyright ownership.
    The ASF licenses this file to you under the Apache License, Version
    2.0 (the "License"); you may not use this file except in compliance
    with the License. You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0 Unless required by
    applicable law or agreed to in writing, software distributed under the
    License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
    CONDITIONS OF ANY KIND, either express or implied. See the License for
    the specific language governing permissions and limitations under the
    License.
-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.manning.junitbook</groupId>
    <artifactId>ch10-maven</artifactId>
    <version>1.0-SNAPSHOT</version>
    <url>http://maven.apache.org</url>
    <name>JUnitBook Chapter 10-Executing JUnit tests with Maven3</name>

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
                <!--
                <configuration>
                    <includes>**/*Test.java</includes>
                </configuration>
                -->
            </plugin>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-site-plugin</artifactId>
                <version>3.7.1</version>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.6.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>5.6.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <developers>
        <developer>
            <name>Catalin Tudose</name>
            <id>ctudose</id>
            <organization>Manning</organization>
            <roles>
                <role>Java Developer</role>
            </roles>
        </developer>
        <developer>
            <name>Petar Tahchiev</name>
            <id>ptahchiev</id>
            <organization>Apache Software Foundation</organization>
            <roles>
                <role>Java Developer</role>
            </roles>
        </developer>
    </developers>
    <description>
        “JUnit in Action III” book, the sample project for the “Running JUnit
        tests from Maven” chapter.
    </description>
    <organization>
        <name>Manning Publications</name>
        <url>http://manning.com/</url>
    </organization>
    <inceptionYear>2019</inceptionYear>
</project>
```
- modelVersion
	- 사용 중인 pom의 모델 버전
- groupId
	- 파일 시스템에서 자바 패키지를 구분하는 역할
	- 조직이나 회사, 그룹의 프로젝트를 그룹화
- artifactId
	- 외부로 알릴 프로젝트의 이름
- version
	- 프로젝트의 현재 버전
- dependencies
	- 필요한 의존성들

## 10.2 Maven 플러그인 활용하기
### 10.2.1 Maven complier 플러그인
- 소스 코드로부터 소프트웨어와 패키지를 컴파일
```XML
<build>
	<plugins>
		<plugin>
			<artifactId>maven-compiler-plugin</artifactId>
			<version>2.3.2</version>
			<configuration>
				<source>1.8</source>
				<target>1.8</target>
			</configuration>
		</plugin>
	</plugins>
<build>
```
	- source, target 파라미터가 없으면 자바 5 버전이 기본값
### 10.2.2 Maven Surefire 플러그인
- Maven 프로젝트에서 단위 테스트 관련 작업을 수행하기 위한 플러그인
```XML
<build>
	<plugins>
		<plugin>
			<artifactId>maven-surefire-plugin</artifactId>
			<version>2.22.2</version>
			<!-- 일부 테스트만 수행하려는 경우 해당 부분을 주석 해제
			<configuration>
				<includes>**/*Test.java</includes>
			</configuration>
			-->
		</plugin>
	</plugins>
</build>
```
	- target 디렉터리 정리 후 소스 코드 테스트 컴파일
	- 이후 test/java 디렉터리에 있는 모든 테스트를 실행

### 10.2.3 Maven 으로 JUnit 리포트 만들기
- Maven은 XML 형식의 JUnit 리포트 생성 가능
- 관습적으로 target/surefire-reports/ 폴더에 테스트 리포트 생성
- maven-surefire-report-plugin 플러그인이 수행
```bash
mvn surefire-report:report
```

## 10.3 한 번에 모아보기
- 명령 프롬프트로 실행 방법
```bash
mvn archetype:generate -DgroupId=comn.testdatasystems.flights -DartifactId=flightsmanagement 
-DarchetypeArtifactId=maven-artifact-mojo
```

## 10.4 Maven의 과제
- Maven의 장점
	- 사고의 프레임을 만들어 둔 뒤 개발자가 그 프레임 안에서 사고하도록 유도하는 것
	- 대부분의 상황에서 Maven은 말도 안 되는 실행을 허용하지 않는다
