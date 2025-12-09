Если ругается на PKIX например из нексус репозиториев то нужно добавить серт нексуса в keystore:
Для java 11: 
```powershell
/c/java/jdk-11.0.24/bin/keytool -importcert -keystore /c/java/jdk-11.0.24/lib/security/cacerts -storepass changeit -file /c/Install/_.services.mts.ru.crt -alias mts-nexus -noprompt
```
Для Java8: 
```powershell
/c/java/jdk-11.0.24/bin/keytool -importcert -keystore /c/java/jdk-11.0.24/lib/security/cacerts -storepass changeit -file /c/Install/_.services.mts.ru.crt -alias mts-nexus -noprompt
```