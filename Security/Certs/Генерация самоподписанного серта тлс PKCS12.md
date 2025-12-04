```
keytool -genkeypair \
  -alias myserver \
  -keyalg RSA \
  -keysize 2048 \
  -storetype PKCS12 \
  -keystore keystore.p12 \
  -storepass password \
  -validity 3650 \
  -dname "CN=localhost, OU=Dev, O=Company, L=City, ST=State, C=RU"
```