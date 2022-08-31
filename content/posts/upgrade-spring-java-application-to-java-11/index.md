---
title: "Upgrading Spring Java application from Java 8 to Java 11"
date: 2022-08-31T10:57:03+02:00
hero: java.png
description: Upgrading Spring Java application from Java 8 to Java 11
draft: false
tags: ["Spring", "Java"]
categories: ["Java"]
---

I recently had to upgrade a Spring Boot application from Java 8 to Java 11. The application also used a very old Spring Boot version (2.1.6).

The experience was not to bad, but I had to do a few things. I updated the pom.xml with the correct Spring Boot and Spring Cloud Version to 2.7.3 for Spring Boot and 2021.0.3 for Spring Cloud.

I also changed the compiler so it would be build in Java 11 like so:

```xml
	<properties>
		<java.version>11</java.version>
		<maven.compiler.target>${java.version}</maven.compiler.target>
		<maven.compiler.source>${java.version}</maven.compiler.source>
    </properties>
```

As well as the maven compiler plugin like so:

```xml
	<build>
		<pluginManagement>
			<plugins>
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-compiler-plugin</artifactId>
					<version>${maven-compiler-plugin.version}</version>
					<configuration>
						<release>${java.version}</release>
						<source>${java.version}</source>
						<target>${java.version}</target>
					</configuration>
				</plugin>
            </plugins>
        </pluginManagement>
    </build>
```

This compiles the code to Java 11

Next up I upgraded the 3rd party libraries to the latest version. The maven versions plugin makes it easy to find the updates.

You execute this command in the project root and it will show you the updated versions.

```bash
mvn versions:display-dependency-updates
```

The next step is to compile the code and make sure all tests are working as expected.

The last step is running the application and make sure it works correctly.

I ran into a problem with Hazelcast and got this warning:

```
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by com.hazelcast.internal.networking.nio.SelectorOptimizer (file:/home/johan/.m2/repository/com/hazelcast/hazelcast/5.1.3/hazelcast-5.1.3.jar) to field sun.nio.ch.SelectorImpl.selectedKeys
WARNING: Please consider reporting this to the maintainers of com.hazelcast.internal.networking.nio.SelectorOptimizer
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

There is an issue open for this error on github: (https://github.com/hazelcast/hazelcast/issues/13151)[https://github.com/hazelcast/hazelcast/issues/13151]


The fix is to update the Java command line and add these options:

```sh
  --add-modules java.se \
  --add-exports java.base/jdk.internal.ref=ALL-UNNAMED \
  --add-opens java.base/java.lang=ALL-UNNAMED \
  --add-opens java.base/java.nio=ALL-UNNAMED \
  --add-opens java.base/sun.nio.ch=ALL-UNNAMED \
  --add-opens java.management/sun.management=ALL-UNNAMED \
  --add-opens jdk.management/com.sun.management.internal=ALL-UNNAMED \
```

After I restarted the application, the warning went away and the application worked on Java 11!





