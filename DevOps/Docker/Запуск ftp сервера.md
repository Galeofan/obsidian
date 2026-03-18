```
docker run -d \
  -p 21:21 \
  -p 30000-30009:30000-30009 \
  -e FTP_USER=test \
  -e FTP_PASS=test \
  -e PASV_ADDRESS=127.0.0.1 \
  -e PASV_MIN_PORT=30000 \
  -e PASV_MAX_PORT=30009 \
  -v c:/ftp:/home/test \
  fauria/vsftpd
```