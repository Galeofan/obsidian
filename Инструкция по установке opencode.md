1) Скачать nodejs portable
2) Добавить Node в PATH (только для пользователя)

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