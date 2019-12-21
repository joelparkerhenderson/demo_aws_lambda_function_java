# Demo AWS lambda function as Java

This demonstration shows:

  * AWS Lambda 
  * Java programming langauage
  * Maven package tool

Contents:

* [Create a project](#create-a-project)
* [Add AWS lamba](#add-aws-lamba)
* [Source](#source)


## Create a project

Create a Maven project:

```sh
mvn -B archetype:generate \
  -DarchetypeGroupId=org.apache.maven.archetypes \
  -DgroupId=com.joelparkerhenderson \
  -DartifactId=demo-aws-lamba-function-hello-world-java
cd demo-aws-lamba-function-hello-world-java
mvn compile
```

If you get this error:

```sh
[ERROR] Source option 6 is no longer supported. Use 7 or later.
[ERROR] Target option 6 is no longer supported. Use 7 or later.
```

Then edit `pom.xml` to upgrade the Java version to the highest available on AWS Lamba, which is currently Java 11, by adding these lines:

```xml
<project …>
  …
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
  </properties>
  …
```

Then retry:

```sh
mvn compile
```

Output:

```sh
…
[INFO] Building demo-aws-lamba-function-hello-world-java 1.0-SNAPSHOT
…
[INFO] BUILD SUCCESS
```


## Add AWS lamba

Add AWS dependency to `pom.xml`:

```xml
<dependency>
  <groupId>com.amazonaws</groupId>
  <artifactId>aws-lambda-java-core</artifactId>
  <version>1.2.0</version>
</dependency>
```

Add build with maven-shade-plugin. The plugin provides the capability to package the artifact in an uber-jar, including its dependencies and to shade - i.e. rename - the packages of some of the dependencies.

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-shade-plugin</artifactId>
      <version>3.2.1</version>
      <configuration>
        <createDependencyReducedPom>false</createDependencyReducedPom>
      </configuration>
      <executions>
        <execution>
          <phase>package</phase>
          <goals>
            <goal>shade</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```


## Source

Create a main class such as `Hello.java` to handle each request:

```java
package com.joelparkerhenderson;

import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.LambdaLogger;
import com.amazonaws.services.lambda.runtime.RequestHandler;

public class Hello implements RequestHandler<RequestClass, ResponseClass> {

    public ResponseClass handleRequest(RequestClass request, Context context){
        String greeting = String.format("Hello %s", request.name);
        return new ResponseClass(greeting);
    }

}
```

Create a AWS Lamba `RequestClass` to serialize input from JSON:

```java
package com.joelparkerhenderson;

public class RequestClass {
    String name;

    public String gettName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }


    public RequestClass(String name) {
        this.name = name;
    }

    public RequestClass() {
    }
}
```

Create a AWS Lamba `ResponseClass` to serialize output to JSON:

```java
package com.joelparkerhenderson;

public class ResponseClass {
    String greeting;

    public String getGreeting() {
        return greeting;
    }

    public void setGreeting(String greeting) {
        this.greeting = greeting;
    }

    public ResponseClass(String greeting) {
        this.greeting = greeting;
    }

    public ResponseClass() {
    }
}
```