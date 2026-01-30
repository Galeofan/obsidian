# Запуск локально
1) Добавить однострочный файл .env по пути: keycloak-phone-provider/docker/.env с содержимым GRAVITEE_API_KEY=...
2) Поправить путь к volumes в docker-compose.yml
3) Запустить скрипт start-dev.sh
# Запуск на проде
1) Задать GRAVITEE_API_KEY=... в environment переменных сервера на котором будет запущен контейнер или задать напрямую в docker-compose.yml
## Список возможных команд запуска и их описание:
```bash
Общие:
--spi-phone-default-service=[dummy|mrc|...]  # Which sms provider
--spi-phone-default-token-expires-in=60  # sms expires ,default 60 second
--spi-phone-default-source-hour-maximum=10 # How many send from ip address sms count in one hour, Zero is no limit. default 10 
--spi-phone-default-target-hour-maximum=3 # How many send to phone number sms count in one hour, Zero is no limit, default 3 
--spi-phone-default-[$realm-]duplicate-phone=false # allow one phone register multi user, default: false
--spi-phone-default-[$realm-]default-number-regex=^\+?\d+$ #Notice: will match after canonicalize number. eg: INTERNATIONAL: +41 44 668 18 00 , NATIONAL: 044 668 18 00 , E164: +41446681800
--spi-phone-default-[$realm-]valid-phone=true # valid phone number, default: true
    #whether to parse user-supplied phone numbers and put into canonical International E.163 format.  _Required for proper duplicate phone number detection_
--spi-phone-default-[$realm-]canonicalize-phone-numbers=E164 #[E164,INTERNATIONAL,NATIONAL,RFC3966], default: "" un-canonicalize;  
    #a default region to be used when parsing user-supplied phone numbers. Lookup codes at https://www.unicode.org/cldr/cldr-aux/charts/30/supplemental/territory_information.html
--spi-phone-default-[$realm-]phone-default-region=US #default: use realm setting's default Locate; 
    #if compatible is true then search user will be use all format phone number 
--spi-phone-default-[$realm-]compatible=false #default: false
    #Prevent 2FA from always happening for a period of time
--spi-phone-default-[$realm-]otp-expires=3600 #default: 60 * 60; 1 hour

Отправка СМС:
	Урлы
--spi-message-sender-service-mrc-sms-send-url=https://host.docker.internal:8983/sms # Урл для отправки смс
--spi-phone-verification-code-default-otp-verify-url=https://host.docker.internal:8983/otp # Урл для подтверждения кода из смс

Http клиент:
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

## Пример команды запуска (нужные значения подставить):
```bash
docker run --name keycloak -p ...:... -e KEYCLOAK_ADMIN=... -e KEYCLOAK_ADMIN_PASSWORD=... galeofan/keycloak:26.2.3_phone-2.5.0 start
--spi-phone-default-service=mrc
--hostname=...
# Если нужно http
--http-port=...
--http-enabled=true
###################
--https-port=...
--spi-phone-default-source-hour-maximum=0
--spi-phone-default-target-hour-maximum=0
--spi-message-sender-service-mrc-sms-send-url=...
--spi-phone-verification-code-default-otp-verify-url=...
```
## Настройки UI
1) Создать новый Realm
2) В Realm settings -> Themes -> Login Theme выбрать тему phone
![[Pasted image 20260129181011.png]]
3) Обязательно отключить проверку обязательности полей email, firstName, lastName в Realm settings -> User Profile -> Edit -> Required field
![[Pasted image 20260129181107.png]]
![[Pasted image 20260129181132.png]]
4) В Authentication создать новый flow
	Обязательно!!! назвать его Browser with phone
	Назначить его как browser flow
	![[Pasted image 20260129180430.png]]
	![[Pasted image 20260129180514.png]]
	![[Pasted image 20260129180600.png]]
5) В Authentication создать новый flow
	Обязательно!!! назвать его Registration with phone
	Назначить его как registration flow
	![[Pasted image 20260129180714.png]]
	![[Pasted image 20260129180754.png]]
	![[Pasted image 20260129180807.png]]
	![[Pasted image 20260129180818.png]]
6) В Clients->[$client-]-> client_scopes задать phone scope by default чтобы в токене возвращалось:
```
  "scope": "profile phone",
  "phone_number_verified": true,
  "phone_number": "+79636022359",
```
![[Pasted image 20260129181401.png]]
7) Задать Authentication -> Required Actions -> Update profile 
![[Pasted image 20260129180930.png]]
