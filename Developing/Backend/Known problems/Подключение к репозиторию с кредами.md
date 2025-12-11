Может быть такое что подключение к репо отбивает 401 если с кредами
Чтобы починить надо убрать из settings.xml все mirror и добавить вручную в ~/.m2 файл settings.xml
Потом перейти в директорию где находится pom проекта и выполнить команду:
`C:\java\apache-maven-3.6.3\bin\mvn -s C:\Users\ACuprun.m2\settings.xml -U clean verify -X`