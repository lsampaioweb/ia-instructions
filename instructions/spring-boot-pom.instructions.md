---
description: "Use when creating or reviewing Spring Boot pom.xml files. Covers parent version, groupId, Java version, required dependencies, and Logback version management."
applyTo: "**/pom.xml"
---

# Spring Boot pom.xml Conventions

## Parent

Always use `spring-boot-starter-parent` as the parent. Pin the latest version explicitly:

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.5.14</version>
  <relativePath />
</parent>
```

## Project Coordinates

- `groupId`: always `io.github.lsampaioweb`
- `artifactId`: use the project name in kebab-case (e.g. `proxmox-installer-endpoint`)
- `version`: start at `0.0.1-SNAPSHOT` for new projects
- `name`: same as `artifactId`

## Java Version

Always use the latest version of Java, for example:

```xml
<properties>
  <java.version>25</java.version>
</properties>
```

## Required Dependencies

Include these in every project unless there is an explicit reason to omit one:

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>

  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>

  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
  </dependency>

  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
  </dependency>

  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>

  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
  </dependency>
</dependencies>
```

## Logback Version Management

Pin Logback explicitly to avoid transitive version conflicts:

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-core</artifactId>
      <version>1.5.21</version>
    </dependency>
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.5.21</version>
    </dependency>
  </dependencies>
</dependencyManagement>
```

## Build Plugins

Always include both plugins. Add Lombok to the annotation processor paths when Lombok is a dependency:

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <annotationProcessorPaths>
          <path>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
          </path>
          <path>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
          </path>
        </annotationProcessorPaths>
      </configuration>
    </plugin>

    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```
