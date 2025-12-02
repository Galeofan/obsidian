#### Основные сущности

**`KeycloakSessionFactory`**
- Глобальная сессия на весь сервер (singleton)
- Хранит реестр всех `Spi` и всех `ProviderFactory`
- Живет пока запущен keycloak
```java
protected volatile Map<Class<? extends Provider>, Map<String, ProviderFactory>> factoriesMap = new HashMap();
Class<? extends Provider> - тип провайдера .class
Map<String, ProviderFactory> - имя провайдера, фабрика

Выглядит примерно так:
{
  PhoneProvider.class: {
      "default": DefaultPhoneProviderFactory
  }
}
```

---

**`ProviderFactory<T extends Provider>`**
- Один инстанс на сервер для каждого провайдера (по `getId()`) (singleton)
- Живёт от старта сервера до его остановки.
- keycloak создаёт одну фабрику провайдера для каждого spi и запихивает в мапу factoriesMap выше
- Отвечает за:
    - `init(Config.Scope)` — чтение конфигурации и инициализация глобальных ресурсов.
    - `postInit(KeycloakSessionFactory)` — работа после инициализации всех фабрик.
    - `create(KeycloakSession)` — создание **конкретного провайдера** под глобальную сессию.
    - `close()` — освобождение глобальных ресурсов на shutdown.

---

**`KeycloakSession`**
- Контекст выполнения: «единица работы» Keycloak (обычно один HTTP-запрос).
- Содержит:
    - ссылки на JPA-контекст, транзакции;
    - текущий `Realm`, `Client`, контекст авторизации;
    - **кэш провайдеров** для этой сессии: `Map<Class<? extends Provider>, Provider>`
- Умеет создавать/кешировать провайдеры через `getProvider(...)`

--- 

**`Provider`**
- Экземпляр на `KeycloakSession` («на каждый запрос»).
- Должен быть легковесным, хранить только state уровня запроса и ссылку на `KeycloakSession`.
- Метод `close()` вызывается при закрытии `KeycloakSession`

- пришёл запрос на авторизацию → создаётся KeycloakSession
- внутри сессии запрашивается UserProvider → создаётся Provider instance
- запрос завершился → Provider закрывается вместе с сессией
- Т.е. жизненный цикл Provider полностью совпадает с жизненным циклом KeycloakSession.
```java
[HTTP Request] 
    ↓
KeycloakSessionFactory.create() → new KeycloakSession
    ↓
KeycloakSession.getProvider(X) → new Provider instance
    ↓
Provider работает в рамках запроса (KeycloakSession)
    ↓
KeycloakSession.close()
    ↓
Provider.close()
```

---

**`ComponentModel` (для многих SPI)**
- Конфигурация провайдера, сохранённая в БД (таблица `COMPONENT` и т.п.).
- Используется для многореализационных SPI (например, User Storage, Event Listener и т.п.).

---

Config.Scope (для Quarkus) включает в себя:
- keycloak.conf
- переменные окружения (`KC_...`)
- аргументы командной строки
- живёт только на время `init()` у фабрики провайдера PhoneProvider

--- 
### Уровень сессии (обычно запрос)
- **Живёт от начала до конца `KeycloakSession`:**
    - `KeycloakSession`
    - `MyProvider` (и другие провайдеры, созданные через `session.getProvider(...)`)
    - JPA `EntityManager`, транзакции и прочее. 

---
#### JPA
session.getProvider(JpaConnectionProvider.class).getEntityManager();
Миграции описываются в token-code-changelog.xml

---
#### HTTP
```bash

Таймауты
--spi-connections-http-client-default-connect-timeout-millis=
--spi-connections-http-client-default-socket-timeout-millis=
--spi-connections-http-client-default-connection-request-timeout-millis=

Пул
--spi-connections-http-client-default-max-connections=200
--spi-connections-http-client-default-max-connections-per-route=50

Прокси
--spi-connections-http-client-default-proxy-host=proxy.server
--spi-connections-http-client-default-proxy-port=8080
--spi-connections-http-client-default-proxy-username=user
--spi-connections-http-client-default-proxy-password=pass

SSL
--spi-connections-http-client-default-disable-trust-manager=true

```

Как проходит отправка смс:
```
PhoneForm -> нажатие на отправить код -> AJAX на TokenCodeResource -> DefaultPhoneProvider.sendTokenCode() -> FullSmsSenderAbstractService.sendMessage() -> MtsbSmsSenderService.sendMessage() ->
```

По нажатию войти:
```
Вызывается дефолтный кейклоаковский POST: /realms/<realm>/login-actions/authenticate?session_code=...&execution=...&client_id=...&tab_id=... -> Он вызывает Authenticator.action() -> validateForm(context, inputData)
```

Валидация кода тут: DefaultPhoneVerificationCodeProvider

Регистрация:
Валидация кода тут: RegistrationPhoneVerificationCode.validate()

---
Изменить логику проверки кода в общей для всех точке: DefaultPhoneVerificationPhoneProvider
public void validateCode(UserModel user, String phoneNumber, String code, TokenCodeType tokenCodeType)
Добавить http запрос в апишку для проверки OTP

Потом в PhoneVerificationCodeProvider заменить if (Validation.isBlank(verificationCode) || tokenCode == null || !tokenCode.getRequestId().equals(verificationCode) на проверку как ниже

И получится что мы ходим за проверкой отп в апишку при регистрации и входе


RegistrationPhoneVerificationCode.validate()
         |
         |--> validateCode()  ← вызывается и при логине
                   |
                   |--> validateOtpExternal()  ← HTTP POST в твой API
                   |
                   |--> tokenValidated()
                   
## ⭐ Шаг 3 — RegistrationForm вместо ручной проверки вызывает validateCode()
Заменяешь блок:
`if (Validation.isBlank(verificationCode) || tokenCode == null || !tokenCode.getRequestId().equals(verificationCode)) {     ... }`
на:
`try {     getTokenCodeService(session).validateCode(         null,                      // user ещё не создан         phoneNumber,         verificationCode,         TokenCodeType.REGISTRATION     ); } catch (Exception ex) {     context.error(Errors.INVALID_REGISTRATION);     errors.add(new FormMessage(FIELD_VERIFICATION_CODE, SupportPhonePages.Errors.NOT_MATCH.message()));     context.validationError(formData, errors);     return; }`
### ✔ Теперь Registration тоже использует validateCode(), а там уже есть вызов validateOtpExternal().