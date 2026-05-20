1) Скачать nodejs portable. РАспаковать в user/tools/node
2) Добавить Node в PATH (только для пользователя). Проверка npm.cmd -v

PowerShell:
```
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned

$userPath = [Environment]::GetEnvironmentVariable("Path", "User")

$newPath = $userPath + ";C:\Users\20And\tools\node"

[Environment]::SetEnvironmentVariable(    
"Path",    
$newPath,    
"User"
)
```

3) Скачать Windows embeddable package (64-bit). Распаковать в user/tools/python
PowerShell:
```
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")  
  
$newPath = $userPath +  
";C:\Users\20And\tools\python" +  
";C:\Users\20And\tools\python\Scripts"  
  
[Environment]::SetEnvironmentVariable(  
"Path",  
$newPath,  
"User"  
)
```
4) Скачать pip.py: https://bootstrap.pypa.io/get-pip.py?utm_source=chatgpt.com. Закинуть в tools/python
5) 