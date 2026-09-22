- **`<dependencies>`** — _что реально подключить_ (попадёт в classpath).
- **`<dependencyManagement>`** — _какие версии/настройки использовать_, когда зависимость где-то будет подключена. Нужно для централизованного управления версиями
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <!-- Версия модели POM. Почти всегда 4.0.0 -->
    <modelVersion>4.0.0</modelVersion>

    <!-- Родитель: дает готовые дефолты по плагинам и dependencyManagement (для Spring Boot) -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.3</version>
        <relativePath/> <!-- искать parent только в репозитории, а не в файловой системе -->
    </parent>

    <!-- Координаты текущего проекта (GAV) -->
    <groupId>ru.galeofan</groupId>
    <artifactId>watchlist</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <!-- Человекочитаемые метаданные -->
    <name>watchlist</name>
    <description>Watchlist project</description>

    <!-- Параметры/версии в одном месте -->
    <properties>
        <!-- Java версия для компиляции -->
        <java.version>21</java.version>

        <!-- Кодировка исходников -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <!-- Управление версиями отдельных библиотек (если нужно явно) -->
        <lombok.version>1.18.34</lombok.version>
    </properties>

    <!-- Реальные зависимости проекта -->
    <dependencies>

        <!-- Spring Boot базовый стартер (в т.ч. автоконфигурация, logging и т.д.) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <!-- Веб: Spring MVC + embedded server (Tomcat по умолчанию) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Lombok: только на этапе компиляции, в рантайме не нужен -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- Тесты: JUnit, AssertJ, Mockito, Spring Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>

            <!-- Компилятор: можно явно закрепить release=21 и включить параметры -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <!-- Самый надежный способ указать целевую версию -->
                    <release>${java.version}</release>
                    <!-- Полезно для дебага/логов, чтобы в bytecode были имена параметров -->
                    <parameters>true</parameters>
                </configuration>
            </plugin>

            <!-- Spring Boot plugin: делает исполняемый jar и умеет "repackage" -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <!-- Явно указать main-class можно при необходимости -->
                    <!-- <mainClass>ru.galeofan.watchlist.WatchlistApplication</mainClass> -->
                </configuration>
            </plugin>

            <!-- Unit-тесты: обычно подтягивается автоматически, но можно настраивать -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
            </plugin>

        </plugins>
    </build>

</project>
```