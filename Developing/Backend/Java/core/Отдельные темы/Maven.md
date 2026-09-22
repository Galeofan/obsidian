---
tags:
  - Java_core
---
### Структура
Папка для хранения исходного кода. - src/main/java 
Папка для хранения тестов. - src/test/java

Все настройки описываются в файле pom.xml

Версия JDK:
`<maven.compiler.source>17</maven.compiler.source>`
`<maven.compiler.target>17</maven.compiler.target>`

### Упаковка в jar-архив
Maven позволяет скомпилированные файлы собрать в архив. Архивы в java имеют расширения JAR - Java Archive.
Для упаковки в архив используется операция **package**

Имя архива maven берет из настроек pom.xml
`<artifactId>tracker</artifactId>`
`<version>1.0</version>`

### Подключение библиотек
Maven упрощает подключение библиотек к проекту.
В файле pom.xml есть блок dependencies. Этот блок содержит библиотеки, которые мы хотим использовать в этом проекте.
```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.9.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>3.23.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
Maven скачивает эти библиотеки сам. Нам не нужно искать их в интернете и хранить в папке проекта.

### Плагины и зависимости в Maven

В Maven есть два важных блока, которые часто используются в конфигурации: `<dependencies>` и `<plugins>`.

- **Блок `<dependencies>`** используется для добавления библиотек, которые ваш проект будет использовать. Эти зависимости могут включать в себя различные внешние библиотеки, необходимые для работы вашего кода. Например, если ваш проект использует библиотеку логирования или утилиту для работы с JSON, вы добавите её в этот блок.
    
- **Блок `<plugins>`** используется для добавления плагинов, которые помогают выполнять различные задачи в процессе сборки проекта. Плагины могут выполнять такие задачи, как проверка стиля кода, сборка артефактов, тестирование и т. д. Плагин Checkstyle — это именно такой инструмент, который помогает проверять оформление кода.

Файл **MANIFEST.MF** внутри JAR-архива содержит метаданные о проекте и его содержимом. Пример его содержимого:
```
Manifest-Version: 1.0
Created-By: Apache Maven 3.6.1
Build-Jdk: 13.0.1
Main-Class: ru.job4j.tracker.StartUI
```