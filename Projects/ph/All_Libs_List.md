## 🎯 Назначение
Сводный список библиотек по **важности** и **частоте применения** в монолите ПХ. Использовать как ориентир при поддержке/рефакторинге.

---

## 🧱 Core (критично и почти везде)

| Группа / Библиотека          | Примеры артефактов                                                                       | Важн. | Част. | Заметки                                  |
| ---------------------------- | ---------------------------------------------------------------------------------------- | ----: | ----: | ---------------------------------------- |
| **Log4j 1.x**                | `log4j:log4j:${log4jVersion}`                                                            |    🟥 |    🟥 | Основное логирование (legacy).           |
| **Apache HttpClient 4.x**    | `org.apache.httpcomponents:httpclient:${httpVersioncomponentsHttpClientVersion}`         |    🟧 |    🟧 | Внешние HTTP‑интеграции (напр. Beeline). |
| **Apache Commons (набор)**   | `commons-lang3`, `commons-io`, `commons-codec`, `commons-text`, `commons-collections(4)` |    🟧 |    🟧 | Повсеместные утилиты.                    |
| **JAXB (API/impl/xjc/core)** | `jaxb-api`, `jaxb-impl`, `jaxb-core`, `jaxb-xjc`                                         |    🟧 |    🟧 | XML маршаллинг/генерация.                |
| **PostgreSQL JDBC**          | `org.postgresql:postgresql:${postgresqlVersion}`                                         |    🟧 |    🟧 | Основной драйвер БД.                     |
| **Jackson 2.x**              | `com.fasterxml.jackson.core:jackson-databind:${jacksonVersion2}`                         |    🟧 |    🟨 | JSON. Зафиксировать **одну** версию.     |

---
## 🗄️ Данные и контейнерные API

| Библиотека | Важн. | Част. | Заметки |
|---|---:|---:|---|
| Hibernate / JPA (`hibernate-core`, `hibernateJpaVersion`, `javaxPersistenceApiVersion`) | 🟨 | 🟨 | ORM в server; в gates чаще JDBC. |
| **c3p0** | 🟨 | 🟨 | Пул коннектов БД. |
| JTA (`jtaVersion`) | 🟨 | 🟨 | Транзакции. |
| JavaEE API (`javaeeApiVersion` / `javaEEVersion`) | 🟨 | 🟨 | Аннотации и контракты. |
| JMS (`jmsVersion`) | 🟨 | 🟨/🟩 | Зависит от модулей и очередей. |

---
## 🔗 SOAP / XML‑безопасность / интеграции

| Библиотека                    |                                                      Важн. | Част. | Заметки |
| ----------------------------- | ---------------------------------------------------------: | ----: | ------- |
| JAX‑WS / SAAJ / Metro         | `jaxwsApiVersion`, `saajImplVersion`, `comSunXmlWsVersion` |    🟨 | 🟨/🟩   |
| XML Security / WSS4J          |                            `xmlsecVersion`, `wss4jVersion` |    🟨 | 🟨/🟩   |
| javax.activation / javax.mail |               `javaxActivationVersion`, `javaxMailVersion` |    🟩 | 🟨/🟩   |
| DOM/XPath                     |                                   `dom4j`, `jaxen`, `jdom` |    🟩 | 🟨      |

---
## ⏱️ Планирование и коммуникации

| Библиотека | Важн. | Част. | Заметки |
|---|---:|---:|---|
| Quartz 1.6.0 | 🟨 | 🟨 | Планировщик задач. |
| Kafka Clients (`kafkaClient`) | 🟨 | 🟨/🟩 | При наличии Kafka‑интеграций. |
| Grizzly / glassfish‑embedded | 🟩 | 🟩 | Встроенный контейнер/тесты. |
| Smack (XMPP) | 🟩 | 🟩 | Точечно. |

---
## 📊 Отчёты / файлы / обмен

| Библиотека | Важн. | Част. | Заметки |
|---|---:|---:|---|
| Apache POI / JXLS / JExcelAPI | 🟨 | 🟨 | Excel отчёты/шаблоны. |
| JasperReports 5.1.2 | 🟨 | 🟨/🟩 | PDF/печать. |
| Velocity 1.7 | 🟨 | 🟨/🟩 | Шаблоны писем/файлов. |
| zip4j / commons-compress | 🟨 | 🟨 | Архивация. |
| JSCH / ftp4j | 🟨 | 🟨/🟩 | SFTP/FTP интеграции. |
| ZXing | 🟩 | 🟩 | QR/штрих‑коды. |

---
## 🌐 UI (только web‑модуль)

| Библиотека | Важн. | Част. | Заметки |
|---|---:|---:|---|
| PrimeFaces 10 / Extensions | 🟨 | 🟨 | JSF UI. |
| RichFaces 4.3.x (+ annotations) | 🟨 | 🟨 | Legacy JSF. |
| Seam 2.3.x (+ Excel/PDF) | 🟨 | 🟨 | Старый стек. |
| Rhino | 🟩 | 🟩 | Редко используется. |

---
## 🧰 Утилиты

| Библиотека | Важн. | Част. | Заметки |
|---|---:|---:|---|
| Guava 18.0 | 🟨 | 🟨 | Коллекции/конкарренси. |
| Reflections | 🟩 | 🟨/🟩 | Скан класспаса. |
| Lombok | 🟨 | 🟨 | Компиляторная аннотации. |
| ICU4J | 🟩 | 🟩 | Локализация/нормализация. |
| libphonenumber | 🟩 | 🟩 | Валидация телефонов. |
| UserAgentUtils | 🟩 | 🟩 | Парсинг UA. |
| Gson / org.json | 🟩 | 🟩 | Точечно; предпочт. Jackson. |
| Javassist | 🟩 | 🟩 | Байткод/прокси, редко явно. |

---
## 🧪 Тесты и качество

| Библиотека | Важн. | Част. |
|---|---:|---:|
| JUnit 4/5, Mockito, PowerMock, H2 | 🟧 | 🟧 |
| PMD / Checkstyle | 🟨 | 🟨 |
| MapStruct | 🟨 | 🟨/🟩 |

---
## ⚠️ Риски и дубли (зафиксировать)

- **Log4j 1.x** — легаси; строго контролировать конфигурацию.  
- **Jackson** — две версии (`2.7.5` и `2.10.3`) → выбрать одну (лучше новее) и унифицировать.  
- **HttpClient** — два поля версий (`httpVersion=4.5.2`, `componentsHttpClientVersion=4.5.6`) → выровнять.  
- **XMLSec 1.4.4 / BouncyCastle 1.46 / iText 2.1.7** — очень старые, возможны уязвимости/лицензии.  
- **Joda‑Time** — legacy; предпочитать `java.time`.  
- **RichFaces / Seam** — устаревший стек UI.  
- **`javax.*` с версией '+'** — непредсказуемые резолвы → зафиксировать.  
- **Дубликаты**: `blueskyVersion` объявлен дважды; различать `javaeeApiVersion` vs `javaEEVersion`.

---
## ✅ Чек‑лист выравнивания версий (минимально болезненно)

1. **Логирование**: подтвердить необходимость Log4j 1.x; при возможности мост на SLF4J или план миграции.  
2. **HttpClient**: установить единый `4.5.6` (или выше в рамках совместимости), убрать второе поле.  
3. **Jackson**: выбрать `2.10.3` (или согласованный BOM), удалить старые артефакты `2.7.5`.  
4. **JAXB**: оставить согласованный стек (`api 2.3.1 / impl 2.3.2 / core 2.3.0.1 / xjc 2.4.0`), убрать дубли.  
5. **Security**: по мере возможности обновить `xmlsec`/`bcprov`; проверить совместимость WSS4J.  
6. **`+` версии**: заменить на фиксированные (особенно `javaxPersistenceApiVersion`, `javaxValidationApiVersion`, `jmsVersion`).  
7. **UI**: фиксировать версии JSF/PrimeFaces; для RichFaces/Seam — документировать ограничения.  

---
## 🥇 Шорт‑лист (приоритезация по сумме важность+частота)
1) Log4j 1.x  
2) Apache HttpClient 4.x  
3) Apache Commons (lang3/io/codec/collections/text)  
4) JAXB (api/impl/core/xjc)  
5) PostgreSQL JDBC  
6) Jackson 2.x (единая версия)  
7) Hibernate/JPA *(если ORM активно)*  
8) c3p0  
9) Quartz  
10) POI/JXLS/JasperReports  
11) JAX‑WS/SAAJ/Metro  
12) WSS4J/XMLSec  
13) Guava  
14) JMS/JavaEE API  
15) FTP/SFTP (JSCH/ftp4j), zip4j
