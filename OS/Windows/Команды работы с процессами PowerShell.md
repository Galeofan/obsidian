netstat -ano | findstr :8081 - последнее число это PID процесса
tasklist /FI "PID eq 20572" - (необязательно) посмотреть инфо о процессе
taskkill /PID 20572 /F - убиваем