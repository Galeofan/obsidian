1. Качаем и ставим JDK c сайта Oracle [https://www.oracle.com/java/technologies/downloads/](https://www.oracle.com/java/technologies/downloads/)
2. Качаем и ставим IDEA Community Edition [https://www.jetbrains.com/idea/download/?section=windows](https://www.jetbrains.com/idea/download/?section=windows)
3. Качаем и устанавливаем GIT [https://gitforwindows.org/](https://gitforwindows.org/)
4. В приложухе GitBash генерим ключи командой ssh-keygen -t rsa -b 4096 -C "your_email@example.com", где указываем свою почту
5. Командой чекаем что агент для работы через SSH запущен: eval "$(ssh-agent -s)"
6. Грузим ключи в агента SSH: ssh-add ~/.ssh/id_rsa
7. Копируем в буфер публичный ключ и грузим его в GitHub: clip < ~/.ssh/id_rsa.pub
8. Авторизуемся в Git: ssh -T git@github.com