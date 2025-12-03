
```
Дефолтный запуск в дев режиме с dummy-sms
docker run --name keycloak-debug -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin -v "C:\Users\20And\kc-data:/opt/keycloak/data" galeofan/keycloak:26.2.3_phone-2.5.0 start-dev --spi-phone-default-service=dummy --spi-phone-default-phone_auth-phone-default-region=RU --spi-phone-default-source-hour-maximum=0 --spi-phone-default-target-hour-maximum=0

Запуск в дев дебаг режиме с dummy-sms
docker run --name keycloak-debug -e JAVA_TOOL_OPTIONS="-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005" -p 8080:8080 -p 5005:5005 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin -v "C:\Users\20And\kc-data:/opt/keycloak/data" galeofan/keycloak:26.2.3_phone-2.5.0 start-dev --spi-phone-default-service=dummy --spi-phone-default-phone_auth-phone-default-region=RU --spi-phone-default-source-hour-maximum=0 --spi-phone-default-target-hour-maximum=0

Запуск в прод режиме с dummy-sms
docker run --name keycloak -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin -v "C:\Users\20And\kc-data:/opt/keycloak/data" galeofan/keycloak:26.2.3_phone-2.5.0 start --spi-phone-default-service=dummy --hostname=localhost --http-port=8080 --http-enabled=true --https-port=0 --spi-phone-default-source-hour-maximum=0 --spi-phone-default-target-hour-maximum=0

Дефолтный запуск в дев режиме с mtsb-sms
docker run --name keycloak-debug -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin -v "C:\Users\20And\kc-data:/opt/keycloak/data" galeofan/keycloak:26.2.3_phone-2.5.0 start-dev --spi-phone-default-service=mtsb --spi-phone-default-phone_auth-phone-default-region=RU --spi-phone-default-source-hour-maximum=0 --spi-phone-default-target-hour-maximum=0 --spi-connections-http-client-default-disable-trust-manager=true

Запуск в дев дебаг режиме с mtsb-sms
docker run --name keycloak-debug -e JAVA_TOOL_OPTIONS="-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005" -p 8080:8080 -p 5005:5005 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin -v "C:\Users\20And\kc-data:/opt/keycloak/data" galeofan/keycloak:26.2.3_phone-2.5.0 start-dev --spi-phone-default-service=mtsb --spi-phone-default-phone_auth-phone-default-region=RU --spi-phone-default-source-hour-maximum=0 --spi-phone-default-target-hour-maximum=0 --spi-connections-http-client-default-disable-trust-manager=true
```

AdminUI
http://localhost:8080/admin/master/console/

Phone_auth
http://localhost:8080/realms/phone_auth/protocol/openid-connect/auth?client_id=multitransfer-ui&response_type=code&redirect_uri=http://localhost:8982/test

**Инструкция для прода:**
Обязательно выключить в Realm settings -> User Profile валидацию полей email, firstName, latName
Обязательно browser flow называть Browser with phone
Обязательно registration flow называть Registration with phone
В clients->multitransfer-ui задать phone scope by default чтобы в токене возвращалось:
```
  "scope": "profile phone",
  "phone_number_verified": true,
  "phone_number": "+79636022359",
```

**Что сделать допом:**
Когда будет апи:
Переименовать поле в бд  вместо requestId
Переименовать дтохи

Закинуть на банковский пк образ

