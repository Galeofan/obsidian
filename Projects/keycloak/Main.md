
```
Дефолтный запуск
docker run -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin -v "C:\Users\20And\kc-data:/opt/keycloak/data" galeofan/keycloak:26.2.3_phone-2.5.0 start-dev --spi-phone-default-service=dummy --spi-phone-default-phone_auth-phone-default-region=RU --spi-phone-default-source-hour-maximum=0 --spi-phone-default-target-hour-maximum=0

Запуск в дебаг режиме
docker run --name keycloak-debug -e JAVA_TOOL_OPTIONS="-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005" -p 8080:8080 -p 5005:5005 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin -v "C:\Users\20And\kc-data:/opt/keycloak/data" galeofan/keycloak:26.2.3_phone-2.5.0 start-dev --spi-phone-default-service=dummy --spi-phone-default-phone_auth-phone-default-region=RU --spi-phone-default-source-hour-maximum=0 --spi-phone-default-target-hour-maximum=0
```

http://localhost:8080/realms/phone_auth/protocol/openid-connect/auth?client_id=multitransfer-ui&response_type=code&redirect_url=localhost:8080

**Инструкция:**
Обязательно выключить в Realm settings -> User Profile валидацию полей email, firstName, latName
Обязательно browser flow называть Browser with phone

**Что сделать допом:**
Надо будет потом пересобрать образ и проверить с включенными userprofile вывод ошибок в лог