1) Скачать nodejs portable
2) Добавить Node в PATH (только для пользователя). Проверка npm.cmd -v
```powershell
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")
$newPath = $userPath + ";C:\Users\20And\tools\node"
[Environment]::SetEnvironmentVariable(    
"Path",    
$newPath,    
"User"
)
```
3) Скачать python(необяз)
```powershell
winget install Python.Python.3.12 --scope user --source winget --accept-package-agreements --accept-source-agreements
```
Проверка:
```powershell
& "$env:LOCALAPPDATA\Programs\Python\Python312\python.exe" --version                                                    
& "$env:LOCALAPPDATA\Programs\Python\Python312\python.exe" -m pip --version         
```
4) Ставим opencode:
```powershell
npm.cmd config set prefix "%USERPROFILE%\npm-global"

$userPath = [Environment]::GetEnvironmentVariable("Path", "User")  
$newPath = $userPath + ";$env:USERPROFILE\npm-global"  
[Environment]::SetEnvironmentVariable(  
"Path",  
$newPath,  
"User"  
)

npm.cmd i -g opencode-ai --verbose

$npmPath = "C:\Users\20And\npm-global"  
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")  
  
[Environment]::SetEnvironmentVariable(  
"Path",  
[Environment]::GetEnvironmentVariable("Path", "User") + ";C:\Users\20And\npm-global",  
"User"  
)
```
Проверка:
```powershell
opencode --version
```
