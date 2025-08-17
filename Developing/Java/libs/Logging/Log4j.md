# 📚 Log4j

## 📝 Описание
Log4j — библиотека для логирования в Java-приложениях. Позволяет писать логи в консоль, файлы, базы данных и другие источники через гибкую систему конфигурации.

## 🚀 Основные классы / интерфейсы
- `Logger` — основной класс для логирования
- `LogManager` — фабрика для получения экземпляров `Logger`
- `Appender` — отвечает за вывод логов (файл, консоль и т.д.)
- `Layout` — форматирует сообщение лога

## 🔑 Ключевые методы
- `logger.info("msg")` — информационное сообщение
- `logger.error("msg", exception)` — сообщение об ошибке с исключением
- `logger.debug("msg")` — отладочные сообщения

## 💡 Примеры использования
```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class Main {
    private static final Logger logger = LogManager.getLogger(Main.class);

    public static void main(String[] args) {
        logger.info("Приложение стартовало");
        try {
            int x = 10 / 0;
        } catch (Exception e) {
            logger.error("Ошибка во время выполнения", e);
        }
    }
}
```

## ⚙️ Настройки / расширения
Пример конфигурации `log4j2.xml`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="info">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
```

## ❗ Подводные камни / особенности
- По умолчанию без конфигурации может не выводить логи или работать в базовом режиме
- Важно отличать версии: Log4j 1.x устарела, рекомендуется использовать Log4j 2.x
- Возможны проблемы производительности при чрезмерно подробном `debug`-логировании

## 🔗 Ссылки
- [Официальная документация](https://logging.apache.org/log4j/2.x/)
- [Конфигурация Log4j](https://logging.apache.org/log4j/2.x/manual/configuration.html)

## 👨‍🦽‍➡️Своими словами
- Библиотека ищет в classpath через системное свойство `java -Dlog4j.configuration=file:/path/to/log4j.properties` путь до файла конфига log4j.properties
- Потом при первом обращении в коде к Logger.getLogger("loggerName") инициализирует файл конфига
- Потом берёт нужные логгеры по ходу работы из параметров `log4j.logger.tinkoff` и т.д.